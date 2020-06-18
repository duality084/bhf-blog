---
title: "Creando Blog Con Hugo"
date: 2020-06-17T17:11:03-03:00
draft: false
author: "Matias, Ramirez"


tags: ["content", "Markdown"]
categories: ["selfhosted"]
featuredImage: "https://d33wubrfki0l68.cloudfront.net/30790d6888bd8af863fb2b5c33a7f337cdbda243/4e867/images/hugo-logo-wide.svg"
lightgallery: true
---

## ¿Que es hugo?

[Hugo](https://gohugo.io/) es un generador de sitios estáticos en el cual mediante un archivo configuración, archivos Markdown o HTML podemos crear sitios o blogs(en este caso este mismo blog). 

## ¿Por que no usar un CMS?

Un CMS o Content Management System es un software el cual nos facilita administrar y publicar contenido en un sitio web, algunos ejemplos pueden ser Wordpress, Joomla, Drupal, etc. Estos sistemas se encuentran programados en distintos lenguajes de programación y constan de varias partes para su correcto funcionamiento como puede ser una base de datos, el lenguaje sobre el cual se ejecuta, las librerías utilizadas por la aplicación, el servidor web el cual se encarga de recibir las peticiones(NGINX, Apache, etc), muchas veces estos sistemas a su vez permiten mediante plugins agregar funcionalidades.

Teniendo esto en cuenta podemos inferir que administrar un CMS puede ser una tarea que consume bastante recursos, y tiempo en mantener todo actualizado y al dia para prevenir intrusiones de seguridad, de todas maneras hablando a nivel seguridad la superficie de ataque en un CMS es muy amplia.

Si bien todo depende de los casos de uso, para mi caso particular un blog, tener un servidor con Linux + MySQL + PHP + servidor web + el tiempo y mantenimiento que conlleva no se justifica, con Hugo simplemente genero un nuevo archivo Markdown para una nueva entrada lo subo a mi repositorio de git luego Hugo se encarga de la creación de todos los archivos estáticos para su publicación y solo necesito de un servidor web(Nginx, Apache,Lighttpd, etc) por lo cual el mantenimiento es mínimo, o simplemente hospedo los archivos mediante [Github Pages](https://pages.github.com/)

---

## Creando un sitio estático

> Aclaración
>
> Esto post no pretende ser un tutorial exhaustivo de como utilizar hugo para mas información por favor diríjase a la [documentación](https://gohugo.io/getting-started/quick-start/)

Hugo consta de un solo binario el cual se puede [descargar](https://github.com/gohugoio/hugo/releases) para distintas plataformas.
Una vez descargado lo descomprimimos en el directorio que querramos y podemos crear un sitio con el siguiente comando.


``` 
hugo new blog
``` 
El comando nos va a crear nuestro sitio con todos los archivos y carpetas necesarias.

``` 
archtypes
content
data
layoutsgit 
resources
static
themes
config.toml
``` 
El archivo config.toml es el encargado de guardar toda la configuración para la construcción de nuestro sitio estático por ejemplo nombre, url, tema a utilizar, estructura de los menús, etc, en mi caso utilizo el tema [LoveIt](https://themes.gohugo.io/loveit/).

Una vez configurado podemos empezar a crear los archivos Markdown e ir escribiendo distintas entradas para nuestro blog, los mismos van dentro la carpeta content y podemos crearlos con el siguiente comando o a mano.
``` 
hugo new posts/example.md
``` 

Por ultimo podremos ver como se ve nuestra pagina web mediante el siguiente comando.

``` 
hugo server
``` 


