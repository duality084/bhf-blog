---
title: "Volatility. Analizando la memoria RAM, NotPinkCon CTF. Parte 1)"
date: 2020-09-02T09:11:03-03:00
author: "Matias, Ramirez"

draft: false

tags: ["forensics","ctf",]
categories: ["forensics","ctf"]
featuredImage: "/images/volatility-ctf-notpinkcon/ctf-flags.jpg"
lightgallery: true

---


El 29 de Agosto se realizó la [NotPinkCon](https://notpinkcon.org/), excelente conferencia de ciberseguridad impartida por mujeres cuyo objetivo principal es incentivar a las mujeres a que participen como oradoras y cada vez sean más las que asisten a eventos de seguridad informática. La jornada del 29 fue excepcional, charlas de un muy buen nivel técnico, con charlas en inglés y castellano, traducción "on the fly" para aquellos que no se sintieran cómodos con el inglés o castellano a través de Discord, todo muy bien organizado. Este año se añadió al evento 2 CTFs, creado por la empresa [Assap](https://www.asapp.com) y otro por [Women's Society of Cyberjutsu](https://womenscyberjutsu.org/) organización que, al igual que la NOTPinkCon, busca acercar y acompañar a las mujeres interesadas en la ciberseguridad. De estos 2 CTFs hablaremos en esta publicación y en una segunda parte.


## Volatility 

[Volatility](https://www.volatilityfoundation.org/) es un un framework escrito en python que nos permite agarrar una "imagen" que se haya tomado de la memoria RAM y hacer un análisis. Con esta imagen y su procesamiento mediante Volatility se puede obtener muchísima información de lo que estaba ocurriendo en el Sistema Operativo al momento en el que se tomó la imagen. Esto suele ser utilizado por personas que se dediquen al análisis forense, respuesta de incidentes, analistas de malware, etc.

Personalmente no había utilizado la herramienta con anterioridad, lo bueno de participar en CTFs es que te "obliga" a utilizar herramientas o aprender nuevos conceptos para resolver los retos. Si bien había leído muchos artículos sobre análisis, con Volatility y la información que se podía obtener, al tener que realizarlo por mi cuenta quedé fascinado con lo que hice y sus posibilidades.


## ASSAP CTF

Luego de la introducción comencemos con lo divertido. El CTF creado por ASSAP consistía de 3 retos, empecemos por los que requerían un análisis de una imagen de la memoria RAM de un equipo infectado con un malware. 

### Primer reto
La consigna era la siguiente:

![CTF0](/images/volatility-ctf-notpinkcon/ctf2.png "OH MY GOD")

Nos dan una imagen de la memoria RAM para descargar y procedemos a analizarla para obtener la ip del host sospechoso al cual se estaban realizando las conexiones por el puerto 80.

Una vez descargada la imagen, pocredemos a analizarla con Volatility en nuestro Kali Linux (ya se encuntra instalado por defecto). Antes de comenzar a obtener lo que se nos solicitó, necesitamos saber qué tipo de sistema operativo estaba corriendo en el equipo. Para eso ejecutamos el comando "volatility -f memory.vmem imageinfo" con el parámetro -f le indicamos qué archivo va a analizar y con imageinfo le indicamos qué información queremos obtener sobre la imagen, el resultado de la ejecución es el siguiente:

```
$volatility -f memory.vmem imageinfo
Volatility Foundation Volatility Framework 2.6
INFO    : volatility.debug    : Determining profile based on KDBG search...
          Suggested Profile(s) : WinXPSP2x86, WinXPSP3x86 (Instantiated with WinXPSP2x86)
                     AS Layer1 : IA32PagedMemoryPae (Kernel AS)
                     AS Layer2 : FileAddressSpace (/home/mramirez/Desktop/NOTPINK-CTF/assap/sigcheck/memory.vmem)
                      PAE type : PAE
                           DTB : 0x319000L
                          KDBG : 0x80544ce0L
          Number of Processors : 1
     Image Type (Service Pack) : 2
                KPCR for CPU 0 : 0xffdff000L
             KUSER_SHARED_DATA : 0xffdf0000L
           Image date and time : 2010-08-15 19:17:56 UTC+0000
     Image local date and time : 2010-08-15 15:17:56 -0400
```

Volatility esta basado en años de investigaciones académicas sobre el análisis avanzado de la memoria RAM, al ser un proyecto Open Source y modular, tiene muchísimas funcionalidades y es capaz de analizar cada aspecto de las aplicaciones y sistema operativo que corría al momento de tomar la imagen. En este caso se nos indica que se sugiere utilizar el "profile" "WinXPSP2x86" o "WinXPSP3x86", cada sistema operativo utiliza y guarda en la memoria RAM los datos de distinta manera, incluso entre versiones del mismo sistema operativo hay diferencias en cómo son almacenados los datos y qué tipo de dato es. En este caso nos sugiere que se analice utilizando el perfil para "Windows XP SP2" en su version de 32 bits. Conociendo esto ya podemos proceder a obtener la información que se nos pide en el reto.

Lo que debíamos obtener es a qué dirección ip se estban realizando conexiones sospechosas sobre el puerto 80, esto lo podemos lograr ejecutando el siguiente comando, "volatility -f memory.vmem --profile=WinXPSP2x86 connscan", en este caso agregamos los parámetros --profile en que especificamos con qué "perfil" se debería analizar la imagen y connscan para que nos muestre las conexiones.

```

$ volatility -f memory.vmem --profile=WinXPSP2x86 connscan
Volatility Foundation Volatility Framework 2.6
Offset(P)  Local Address             Remote Address            Pid
---------- ------------------------- ------------------------- ---
0x02214988 172.16.176.143:1054       193.104.41.75:80          856
0x06015ab0 0.0.0.0:1056              193.104.41.75:80          856
```
El resultado, por lo que pueden ver, es muy completo. Tenemos direccion local desde la cual se está realizando la conexión, la dirección remota y el identifcador del proceso(PID) que está realizando dicha conexion, en este caso solo se nos pide la ip. así que el flag para resolver el reto es "193.104.41.75".
### Segundo reto
Otro de los retos era obtener el PID del proceso que realiza la conexión. Esto lo obtuvimos con el comando anterior, se puede observar que el PID es el 856.

![CTF1](/images/volatility-ctf-notpinkcon/ctf.PNG "PID")

A modo de demostración podemos obtener también el nombre del proceso que estaba realizando las conexiones con el siguiente comando: "volatility -f memory.vmem --profile=WinXPSP2x86 pslist". En este caso agregamos el parametro pslist que nos va a mostrar la lista de todos los procesos en ejecución.
```
$ volatility -f memory.vmem --profile=WinXPSP2x86 pslist
Volatility Foundation Volatility Framework 2.6
Offset(V)  Name                    PID   PPID   Thds     Hnds   Sess  Wow64 Start                          Exit                          
---------- -------------------- ------ ------ ------ -------- ------ ------ ------------------------------ ------------------------------
0x810b1660 System                    4      0     58      379 ------      0                                                              
0xff2ab020 smss.exe                544      4      3       21 ------      0 2010-08-11 06:06:21 UTC+0000                                 
0xff1ecda0 csrss.exe               608    544     10      410      0      0 2010-08-11 06:06:23 UTC+0000                                 
0xff1ec978 winlogon.exe            632    544     24      536      0      0 2010-08-11 06:06:23 UTC+0000                                 
0xff247020 services.exe            676    632     16      288      0      0 2010-08-11 06:06:24 UTC+0000                                 
0xff255020 lsass.exe               688    632     21      405      0      0 2010-08-11 06:06:24 UTC+0000                                 
0xff218230 vmacthlp.exe            844    676      1       37      0      0 2010-08-11 06:06:24 UTC+0000                                 
0x80ff88d8 svchost.exe             856    676     29      336      0      0 2010-08-11 06:06:24 UTC+0000                                 
0xff217560 svchost.exe             936    676     11      288      0      0 2010-08-11 06:06:24 UTC+0000                                 
0x80fbf910 svchost.exe            1028    676     88     1424      0      0 2010-08-11 06:06:24 UTC+0000                                 
0xff22d558 svchost.exe            1088    676      7       93      0      0 2010-08-11 06:06:25 UTC+0000                                 
0xff203b80 svchost.exe            1148    676     15      217      0      0 2010-08-11 06:06:26 UTC+0000                                 
0xff1d7da0 spoolsv.exe            1432    676     14      145      0      0 2010-08-11 06:06:26 UTC+0000                                 
0xff1b8b28 vmtoolsd.exe           1668    676      5      225      0      0 2010-08-11 06:06:35 UTC+0000                                 
0xff1fdc88 VMUpgradeHelper        1788    676      5      112      0      0 2010-08-11 06:06:38 UTC+0000                                 
0xff143b28 TPAutoConnSvc.e        1968    676      5      106      0      0 2010-08-11 06:06:39 UTC+0000                                 
0xff25a7e0 alg.exe                 216    676      8      120      0      0 2010-08-11 06:06:39 UTC+0000                                 
0xff364310 wscntfy.exe             888   1028      1       40      0      0 2010-08-11 06:06:49 UTC+0000                                 
0xff38b5f8 TPAutoConnect.e        1084   1968      1       68      0      0 2010-08-11 06:06:52 UTC+0000                                 
0x80f60da0 wuauclt.exe            1732   1028      7      189      0      0 2010-08-11 06:07:44 UTC+0000                                 
0xff3865d0 explorer.exe           1724   1708     13      326      0      0 2010-08-11 06:09:29 UTC+0000                                 
0xff3667e8 VMwareTray.exe          432   1724      1       60      0      0 2010-08-11 06:09:31 UTC+0000                                 
0xff374980 VMwareUser.exe          452   1724      8      207      0      0 2010-08-11 06:09:32 UTC+0000                                 
0x80f94588 wuauclt.exe             468   1028      4      142      0      0 2010-08-11 06:09:37 UTC+0000                                 
0xff224020 cmd.exe                 124   1668      0 --------      0      0 2010-08-15 19:17:55 UTC+0000   2010-08-15 19:17:56 UTC+000
```

Si buscamos en la columna PID el número "856" y luego observamos en la misma línea la columna "Name" encontramos que el nombre del proceso que está realizando las conexiones es "svchost.exe".

### Tercer reto
Por último, el 3er reto. En este se nos habla de [SIGCHECK](https://docs.microsoft.com/en-us/sysinternals/downloads/sigcheck), una de las tantas maravillosas herramientas de sysinternals(Process Explorer, procdump, psexec, etc), con esta herramienta podemos analizar archivos ejecutables y obtener su versión, timestamp, firma digital, hash, etc. Pero una de sus mejores características es comprobar el archivo en [VirusTotal](https://www.virustotal.com) y saber si el archivo se trata de un malware. El reto era el siguiente:
![CTF3](/images/volatility-ctf-notpinkcon/ctf3.png "SIGCHECK")

Nos dan un enlace para descargar un archivo con la extensión .numbers. Luego de una simple búsqueda en Google, me percato de que el archivo pertenece al programa "Numbers" de la suite "iWork", sería el equivalente de Excel en Microsoft Office. Buscamos un conversor para transformarlo a un formato que podamos leer. Encontré la página [https://cloudconvert.com/numbers-to-csv/](https://cloudconvert.com/numbers-to-csv) la cual nos permite, como lo dijimos antes, convertir el archivo a CSV.

![Numbers](/images/volatility-ctf-notpinkcon/numberscon.png "Numbers Converter")

Una vez convertido podemos ver su contenido con nano(?).

![nano](/images/volatility-ctf-notpinkcon/csv.PNG "CSV nano")

Bueno, no se ve muy bonito y no es muy fácil de leer, pero en base a la consigna que tenemos en el reto, lo que necesitamos saber es el nombre del archivo que disparó las alertas. Sabiendo eso, podemos procesar el archivo con las grandiosas herramientas que los Dioses de UNIX nos dieron cuando el mundo fue creado.
![UnixGods](/images/volatility-ctf-notpinkcon/Ken_Thompson_and_Dennis_Ritchie--1973.jpg "Ken Thompson y Dennis Ritchie")

Sí, estamos hablando de procesar el archivo con los míticos comandos cat, awk y grep. Sabemos que el archivo es un CSV separado por comas. Y lo que estamos buscando son los archivos que hicieron saltar las "alarmas", el archivo CSV que obtuvimos luego de la conversión se trata de la salida del comando SIGCHECK con los resultados luego de analizar la carpeta "z:\windows_mount\windows\system32" y todos sus archivos. El archivo CSV consta de varias columnas.
```
Path,Verified,Date,Publisher,Company,Description,Product,Product Version,File Version,Machine Type,MD5,SHA1,PESHA1,PESHA256,SHA256,IMP,VT detection,VT link
```
Las que nos interesan son "Path" para saber el nombre del archivo sospechoso y "VT Detection" para saber si el archivo tiene detección como malware, por los antivirus que analizan el archivo en VirusTotal. En la columna "VT Detection", nos aparece la cantididad de detecciones y la cantidad de soluciones antivirus que analizaron el archivo separado por el caracter "|". Con el comando grep podemos filtrar solo los archivos cuyas líneas no contengan "0|" y luego con awk imprimir solo las columnas que nos interesan.

```
cat sigcheck-win7-32-blabla01.csv | grep -v "0|" |awk 'BEGIN {FS = ","}; {print $1 "," $17 }'
Path,VT detection
z:\windows_mount\windows\system32\TPVMW32.dll,1|60
z:\windows_mount\windows\system32\pe.exe,1|60
z:\windows_mount\windows\system32\f-response-ent.exe,3|62
z:\windows_mount\windows\system32\TPVMMon.dll,1|62
z:\windows_mount\windows\system32\hydrakatz.exe,42|61
z:\windows_mount\windows\system32\sekurlsa.dll,33|56
```
Maravilloso, de las 2081 líneas del archivo original solo nos quedaron 6 líneas, observamos que el archivo con más detecciones es "hydrakatz.exe", con 42 detecciones. El flag para el reto es "hydrakatz.exe", sí, ya se lo que están pensando, de seguro era más fácil llevar el CSV a Excel o Calc y filtrar los resultados. ¿Pero esto no se trata de aprender cosas nuevas?

### Bonus track

Cuando uno lee las soluciones de los CTFs se suele pensar que la persona que lo escribió es un "genio" o "genia", que tiene las respuestas a todas las preguntas. La verdad es que no es así, como comenté en un principio no había utilizado nunca Volatility, y no conocía con anterioridad SIGCHECK y me llevé un gran aprendizaje haciendo este CTF. La manera de resolver los retos es perserverar e intentar distintas soluciones a los problemas. Mientras realizaba el primer reto que expuse no me tomaba el "flag" "193.104.41.75", por lo que procedí a tomar otros caminos pensando que me estaba equivocando en la respuesta. Eso me llevó a un [cheatsheet](https://digital-forensics.sans.org/media/volatility-memory-forensics-cheat-sheet.pdf) sobre Volatility y en especial a una manera de extraer los archivos que se podían encontrar en la memoria RAM.

![CheatSheat](/images/volatility-ctf-notpinkcon/cheatsheetv.png "Volatility is AWSOME!")

Lo que decidí hacer era ver si podía dumpear todos los archivos ejecutables que se pudieran encontrar, utilizar SIGCHECK para comprobar los archivos contra VirusTotal, saber cuál era el malware que estaba realizando las conexiones y encontrar cuál era la IP a la que se conectaba. Para "dumpear" todos los ejecutables utilicé el siguiente comando:
```
volatility -f memory.vmem --profile=WinXPSP2x86 dumpfiles -n -i -r \\.exe --dump-dir=./dump3

Volatility Foundation Volatility Framework 2.6
ImageSectionObject 0x80ff85c0   544    \Device\HarddiskVolume1\WINDOWS\system32\smss.exe
ImageSectionObject 0x80f2e828   608    \Device\HarddiskVolume1\WINDOWS\system32\csrss.exe
DataSectionObject 0xff12bb40   632    \Device\HarddiskVolume1\WINDOWS\system32\sdra64.exe
ImageSectionObject 0x80f6e800   632    \Device\HarddiskVolume1\WINDOWS\system32\winlogon.exe
DataSectionObject 0x80f6e800   632    \Device\HarddiskVolume1\WINDOWS\system32\winlogon.exe
ImageSectionObject 0xff39f140   676    \Device\HarddiskVolume1\WINDOWS\system32\services.exe
ImageSectionObject 0x80f05028   688    \Device\HarddiskVolume1\WINDOWS\system32\lsass.exe
ImageSectionObject 0xff386440   844    \Device\HarddiskVolume1\Program Files\VMware\VMware Tools\vmacthlp.exe
ImageSectionObject 0xff379f90   856    \Device\HarddiskVolume1\WINDOWS\system32\svchost.exe
ImageSectionObject 0x81000f28   1432   \Device\HarddiskVolume1\WINDOWS\system32\spoolsv.exe
ImageSectionObject 0xff3b9c68   1668   \Device\HarddiskVolume1\Program Files\VMware\VMware Tools\vmtoolsd.exe
ImageSectionObject 0xff3c04e8   1788   \Device\HarddiskVolume1\Program Files\VMware\VMware Tools\VMUpgradeHelper.exe
ImageSectionObject 0x80f00af8   1968   \Device\HarddiskVolume1\Program Files\VMware\VMware Tools\TPAutoConnSvc.exe
ImageSectionObject 0x80f75b68   216    \Device\HarddiskVolume1\WINDOWS\system32\alg.exe
ImageSectionObject 0x80f31ba0   888    \Device\HarddiskVolume1\WINDOWS\system32\wscntfy.exe
ImageSectionObject 0xff379028   1084   \Device\HarddiskVolume1\Program Files\VMware\VMware Tools\TPAutoConnect.exe
ImageSectionObject 0xff1e1480   1732   \Device\HarddiskVolume1\WINDOWS\system32\wuauclt.exe
DataSectionObject 0xff1e1480   1732   \Device\HarddiskVolume1\WINDOWS\system32\wuauclt.exe
ImageSectionObject 0x80fff578   1724   \Device\HarddiskVolume1\WINDOWS\explorer.exe
DataSectionObject 0x80fff578   1724   \Device\HarddiskVolume1\WINDOWS\explorer.exe
ImageSectionObject 0x80fb5e58   432    \Device\HarddiskVolume1\Program Files\VMware\VMware Tools\VMwareTray.exe
DataSectionObject 0x80fb5e58   432    \Device\HarddiskVolume1\Program Files\VMware\VMware Tools\VMwareTray.exe
ImageSectionObject 0x80fb5138   452    \Device\HarddiskVolume1\Program Files\VMware\VMware Tools\VMwareUser.exe
DataSectionObject 0x80fb5138   452    \Device\HarddiskVolume1\Program Files\VMware\VMware Tools\VMwareUser.exe
```

El comando nos dejó todos los archivos encontrados en la carpeta "dump3", en el nombre podemos observar el identificador de proceso al que está asociado el archivo y el nombre del archivo.


![FilesDump](/images/volatility-ctf-notpinkcon/dump.png "Files Dump")


Como me gusta mantenerme en Linux, ejecutamos Sigcheck con wine (ejecutar la version de 64 bits de wine junto con la version 64bits de sigcheck) para realizar el escaneo de los archivos que obtuvimos. Luego de ejectuar sigcheck de la siguiente forma:
```
sigcheck64.exe -v -s -c dump3

```

Obtenmos el siguiente resultado (se quitaron filas y columnas sin relevancia para una optima legibilidad):
```
Path,Verified,Date,Publisher,Company,Description,Product,Product Version,File Version,Machine Type,VT detection,VT link

"file.632.0xff37f270.sdra64.exe.dat","32-bit","61|72","https://www.virustotal.com/gui/file/b38783113eda00bbe864d54fda9db97e36ee9fc8e4509e3dc71478a46250f498/detection/"

```

Como vemos, obtenemos un archivo llamado "sdra64.exe" con detección en 61 de 72 motores antivurus, si procedemos a acceder al [enlace](https://www.virustotal.com/gui/file/b38783113eda00bbe864d54fda9db97e36ee9fc8e4509e3dc71478a46250f498/detection/) de VirusTotal con el analiss de la muestra vemos lo siguiente:

![Virus total](/images/volatility-ctf-notpinkcon/virustotal.png "Resultado Virustotal")

Y en el apartado ["behavior"](https://www.virustotal.com/gui/file/b38783113eda00bbe864d54fda9db97e36ee9fc8e4509e3dc71478a46250f498/details) podemos observar lo siguiente.

![Virus total- Comportamiento](/images/volatility-ctf-notpinkcon/behaviorvt.png "Malware comportamiento")

Observamos que el malware efectivamente se comunica a la ip "192.104.41.75" por el puerto 80 utilizando el protocolo HTTP para obtener el archivo "75.bro". Luego de este descubrimiento, y al estar 99% seguro de que el flag que estaba poniendo era el correcto mediante este segundo experimento, decidí comunicarme por Discord en el canal de la NOTPinkCON con la gente de ASSAP para informarles que no me tomaba correctamente el flag, la gente de ASSAP respondió de manera casi inmediata y pude validar el flag correctamente.

De esta manera termina esta primer entrega: contento de aprender y tener por primera vez un "hands on" con volatility, haber usado por primera vez la herramienta sigcheck, repasando y afianzando los conocimientos obtenidos escribiendo y compartiendo este post con ustedes. Pronto la segunda entrega sobre algunos retos del CTF principal de la NotPinkCon!


Happy hacking!
