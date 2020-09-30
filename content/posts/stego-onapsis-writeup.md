---
title: "WriteUp CTF Stego de Onapsis - EKOPARTY"
date: 2020-09-30T16:02:03-03:00
author: "bronxi. memoryempty, Matías Ramírez"

draft: true

tags: ["writeup","stego","ekoparty"]
categories: ["writeup","stego"]
featuredImage: "https://blog.eccouncil.org/wp-content/uploads/2020/05/Steganography.jpg"
lightgallery: true

---


El año pasado fue mi primer eko, después de bastante tiempo había retomado mi pasión por la seguridad informática. Sobre todo por entender cómo funcionan las cosas y cómo podría utilizarse ese funcionamiento para otros fines.
A diferencia del año pasado, esta eko me encontró participando de un team (BHF) y además con algunas nociones para poder encarar los CTF’s, cosa que el año anterior no hubiese imaginado.
Shebi nos anotó como equipo en dos CTF’s: Pucará y Onapsis y entramos a ver cada uno de los challenges a ver por dónde empezar y qué podíamos hacer. No nos imaginábamos que íbamos a rankear en ambos, simplemente era un desafío gustoso que tomábamos como equipo.
Después de terminar el juego de abrir los candados de Naranja, me aboqué al CTF de Onapsis. Uno de los ch4ll3ng3 que más me gustó fue el de stego. Les invito a leer el writeup a continuación.
 

![Steg Flag](/images/stego-onapsis-writeup/stegoctf.jpg "Steg FLAG")

Cómo se observa en la imagen, hace referencia al uso de la clásica cortapluma Victorinox, esto da una pista de que se necesitarán varias herramientas. El archivo descargado es “in_plain_sight.tar.xz”. Al extraerlo se encuentra una imagen: “in_plain_sight.jpg”. Al abrirla, es una captura de Toy Story 2, de nuestros héroes cruzando la calle hacia la juguetería.

Abro paréntesis, literal (si recién comienzan en el mundo de la esteganografía, les recomiendo este artículo: https://hackinglethani.com/es/esteganografia/ )

![Imagen](/images/stego-onapsis-writeup/in_plain_sight.jpg "Imagen de Toy Story 2")

Lo primero que hice fue ejecutar el comando "strings" sobre el archivo, pero no obtuve nada interesante. También le pasé Binwalk, sin resultados. Algo me decía, por el título del ch4ll3ng3, que se trababa de encontrar un mensaje oculto en las capas de la imagen. Para esto utilicé la herramienta Stegsolve (https://github.com/zardus/ctf-tools/blob/master/stegsolve/install) en Kali/Linux:

```
java -jar stegsolve.jar

```

Al ejecutarse, abrí la imagen y justo cuando iba pasando capaz por capaz, apareció memoryemptpy desde atrás y dijó ahí está:

![th1s_1s_n0t_th3_fl4g](/images/stego-onapsis-writeup/thisisnottheflag.jpg "th1s_1s_n0t_th3_fl4g")

Pero el mensaje era sincero, no se trataba del flag. Entonces, ¿para qué era? Un rabbit hole seguro que no.




1- Revisión de capaz de la imagen con Stegsolve.
2- Búsqueda de archivos ocultos protegidos por clave con Steghide.
3- Descifrado de withespace con Snow.

