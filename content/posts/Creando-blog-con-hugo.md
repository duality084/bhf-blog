---
title: "Creando Blog Con Hugo"
date: 2019-08-14T17:11:03-03:00
draft: false
author: "Matias, Ramirez"


tags: ["content", "Markdown"]
categories: ["selfhosted"]
featuredImage: "https://d33wubrfki0l68.cloudfront.net/30790d6888bd8af863fb2b5c33a7f337cdbda243/4e867/images/hugo-logo-wide.svg"
lightgallery: true
---

## ¿Que es hugo?

[Hugo](https://gohugo.io/) es un generador de sitios estáticos en el cual mediante un archivo configuración, archivos Markdown o HTML podemos crear sitios o blogs(en este caso este mismo blog). 

¿Por que no usar otra herramienta como Wordpress?

Wordpress es un gestor de contenidos o CMS que consta de muchas "partes" para que su funcionamiento sea correcto, estas "partes"(servidor de base de datos, php, plugins) hace que muchas veces su mantenimiento sea costoso en tiempo y que consuma muchos recursos para una tarea tan simple como publicar contenido de manera periódica en un sitio web, como en todo hay casos y casos en los cuales las distintas herramientas se ajustan mejor a distintos escenarios. Para mi caso particular, un blog, tener un servidor mysql + php + servidor web + el tiempo y mantenimiento que conlleva no se justifica, con Hugo simplemente genero un nuevo archivo Markdown para una nueva entrada lo subo a mi repositorio de git para mantenerlo versionado y luego Hugo se encarga de la creación de todos los archivos estáticos para su publicación y solo necesito de un servidor web(nginx, apache,lighttpd, etc) por lo cual el mantenimiento es mínimo.

---

## Creando un sitio estático

Hugo consta de un solo binario el cual se puede [descargar](https://github.com/gohugoio/hugo/releases) para distintas plataformas.
Una vez descargado lo descomprimimos en el directorio que querramos y podemos crear un sitio con el siguiente comando.

``` 
hugo new blog
``` 
El comando nos va a crear nuestro sitio con todos los archivos necesarios y carpetas necesarios.

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
El archivo config.toml es el encargado de guardar toda la configuración para la construcción de nuestro sitio estático por ejemplo nombre, url, tema a utilizar, estructura de los menús, etc, en mi caso utilizo el tema [Nix](https://themes.gohugo.io/hugo-theme-nix/).

Una vez configurado podemos empezar a crear los archivos Markdown e ir escribiendo distintas entradas para nuestro blog, los mismos van dentro la carpeta content y podemos crearlos con el siguiente comando o a mano.
``` 
hugo new posts/example.md
``` 



