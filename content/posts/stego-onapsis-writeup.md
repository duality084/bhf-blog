---
title: "WriteUp CTF Stego de Onapsis - EKOPARTY"
date: 2020-09-30T16:02:03-03:00
author: "bronxi. memoryempty, Matías Ramírez"

draft: false

tags: ["writeup","stego","ekoparty"]
categories: ["writeup","stego"]
featuredImage: "/images/stego-onapsis-writeup/Steganography.jpg"
lightgallery: true

---

El año pasado fue mi primer eko, después de bastante tiempo, retomé mi pasión por la seguridad informática. Esa curiosidad, sobre todo por entender cómo funcionan las cosas y cómo podría utilizarse ese funcionamiento para otros fines.
A diferencia del año pasado, esta eko me encontró participando de un team (BHF) y además con algunas nociones para poder encarar los CTF’s, cosa que el año anterior no hubiese imaginado.
Shebi nos anotó como equipo en dos CTF’s: *Pucará* y *Onapsis*. Entramos a ver cada uno de los ch4ll3ng3s a ver por dónde empezar y qué podíamos hacer. No nos imaginábamos que íbamos a rankear en ambos, simplemente era un desafío gustoso que tomábamos como equipo.

Abro paréntesis, literal (si recién comienzan en el mundo de la esteganografía, les recomiendo este artículo: https://hackinglethani.com/es/esteganografia/)

Después de terminar el juego de abrir los candados de *Naranja*, me aboqué al CTF de *Onapsis*. Uno de los ch4ll3ng3 que más me gustó fue el de stego. Les invito a leer el writeup a continuación.
 
## Premisa del ch4ll3ng3

![Steg Flag](/images/stego-onapsis-writeup/stegoctf.jpg "Steg FLAG")


Como se observa en la imagen, hace referencia al uso de la clásica cortapluma Victorinox, esto da una pista de que se necesitarán varias herramientas para su resolución. El archivo descargado es “in_plain_sight.tar.xz”. Al extraerlo hay una  imagen: “in_plain_sight.jpg”. Es una captura de una escena de Toy Story 2, cuando nuestros héroes cruzan la calle hacia la juguetería.


![Imagen](/images/stego-onapsis-writeup/in_plain_sight.jpg "Imagen de Toy Story 2")

## 1° hallazgo (stegsolve)

Lo primero que hice fue ejecutar el comando "strings" sobre el archivo, pero no obtuve nada interesante. También le pasé Binwalk, sin resultados. Algo me decía, por el título del ch4ll3ng3, que se trababa de encontrar un mensaje oculto en las capas de la imagen. Para esto utilicé la herramienta Stegsolve (https://github.com/zardus/ctf-tools/blob/master/stegsolve/install) en Kali/Linux:

```
# java -jar stegsolve.jar

```

Al ejecutarse, abrí la imagen y justo cuando iba pasando capa por capa, apareció memoryempty desde atrás y dijó ahí está, y tenía razón:

![th1s_1s_n0t_th3_fl4g](/images/stego-onapsis-writeup/thisisnottheflag.jpg "th1s_1s_n0t_th3_fl4g")


## 2° hallazgo (steghide)

Nos emocionamos, pero el mensaje era sincero, no se trataba del flag. Entonces, ¿para qué era? Un rabbit hole seguro que no.
Ya había probado con Binwalk, que no encontró archivos ocultos, o eso pensé. Di con una herramienta que busca archivos ocultos protegidos con clave. Se trata de "Steghide" (https://pkg.kali.org/pkg/steghide):

```
# steghide extract -sf in_plain_sight.jpg
```

Esto me solicitó una clave, y adivinen cuál era, ¡sí! th1s_1s_n0t_th3_fl4g! Al introducir la clave, se genera en el directorio el archivo "flag.txt". Al leerlo nos da el siguiente mensaje:

```
# cat flag.txt
What?!			 	    	      	       	   	  	       
Flag was right here next to me!	     	    	 	      		    
I promise!      	       	   		     	 	     	    
It was super happy when Snow White and the 7 dwarfs visited   		    
Wonder what happenned...    	   	       	   	       	       	 
I know it's a little bit shy...    		  	     	  	       
Maybe it's hiding...       	 	 	  	 	     	     
There aren't that many places to hide, though...	  	  	   
Maybe in plain sight?    	      	     	     	       	       
After all, it's said to be one of the best places to hide	
```

## 3° Th1s_1s_th3_fl4g! (stegsnow)

Dice que el flag está junto al texto y no es visible. También habla de Blancanieves... recordé un taller sobre esteganografía que había visto el día anterior:


Taller de CTF I: Criptografía y Esteganografía - Víctor Villar
{{< youtube LsDOpDQtu_8 >}}


Vi la ocultación de mensajes mediante el método "whitespace", que no es visible a la vista, pero con cualquier editor de texto en terminal es posible observar en color rojo aquellos lugares en blanco. Probé con nano y así era. Luego de cada frase había varias whitespaces. En el mismo taller, recomendaban la herramienta "Snow" para descifrar estos mensaje, y como hablaba de Blancanieves... ipso facto. Al no correr bien "Snow", opté por "Stegsnow":

```
# stegsnow -C flag.txt
s0_m4ny_w4yZ_0f_h1d1ng_d4t4

```
¡Qué alegría! Lo pasamos a sha256, dio flag correcto :) y sumamos 150 puntos.
