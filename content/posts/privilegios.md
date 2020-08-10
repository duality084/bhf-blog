---
title: "Checklist para escalar privilegios desde www-data"
subtitle: "Entramos, ¿y ahora qué hacemos?"
date: 2020-08-09T19:48:20-03:00
lastmod: 2020-08-09T19:48:20-03:00
draft: false
author: "bronxi"
authorLink: ""
description: ""

tags: ["privilegios, escalar, root, sudo"]
categories: []

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: "images/checklist.gif"
featuredImagePreview: ""

toc:
  enable: true
math:
  enable: false
lightgallery: false
license: ""
---

# Checklist de posibilidades para escalar privilegios desde www-data

# “Entramos, ¿y ahora qué hacemos?”

Pregunta que me hice muchas veces cuando empecé a participar en distintos CTF (Capture The Flag). Leí libros, sites, foros, hilos, videos (¡sí!, leo videos) y quedaba fascinado, pero la teoría puede ser muy bonita (o súper boring) si no se lleva a la práctica. Por eso empecé a participar de CTF y a principios de este año logré registrarme en HTB. Al lograr acceso a alguna máquina intentaba hacer algo, como leer archivos sensible y ejecutar determinados comandos; no lograba mucho ya que el usuario que utilizaba tenía muy bajos privilegios.
Esto sucede en muchos CTF, ingresás al servidor web como el usuario público www-data, lográs ejecutar comandos pero es muy poco lo que se puede hacer. Al principio iba y venía entre directorios. Listaba para ver qué carpetas y archivos que podía leer o modificar. Existen varias cuestiones que podemos probar, presento las más utilizadas y, en general, las que dan resultado en los CTF. Se trata de cinco pruebas en la lista: Si soy sudoers, Binarios SUID, Archivos w & x, Procesos de servicios y Kernel vulnerable.

Puedo ejecutar comandos, ahora pruebo:

## 1° - Si soy sudoers (estás en la lista en /etc/sudoers)

En uno de esos retos aprendí que si conocemos los comandos que podemos ejecutar como root, podemos abusar de ellos (Shebi’s credit). Entonces, listamos aquellos comandos con privilegios de root que tenemos permitido ejecutar sin contraseña. En otras palabras, qué comandos puede ejecutar como súper usuario el usuario www-data, o cualquier otro con el que ingresemos. Para esto, ejecutamos el comando

sudo -l

Si existen comandos que podemos ejecutar como root sin uso de password, podemos abusar de estos. Imaginémos el siguente escenario:

sudo -l
'bronxi@flux::~$ sudo -l

Coincidiendo entradas por defecto para bronxi en flux:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

El usuario bronxi puede ejecutar los siguientes comandos en flux:

 (root) NOPASSWD: /usr/bin/find
 (root) NOPASSWD: /usr/bin/vim
 (root) NOPASSWD: /usr/bin/awk'

Existen tres comandos que podemos ejecutar como root sin la necesidad de la clave: find, vim y awk. ¿Cómo abusamos de ellos? Encontramos información detallada de cualquiera de estos tres comandos y muchísimos más en

https://gtfobins.github.io

A modo de ejemplo, seleccionemos “vim”, ejecutamos

vim -c ':!/bin/sh'
#whoami
root
#

Fácil, ¿cierto?
En breve publicaré la siguiente prueba de nuestra lista mágica.


*Para realizar este lista, amplié cada punto mencionado de la publicación que tomé como base del este blog, que recomiendo, https://failingsilently.wordpress.com/2017/08/07/privesc/
