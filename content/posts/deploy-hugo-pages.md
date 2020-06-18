---
title: "Automatizando el deploying del blog con Hugo y Travis."
date: 2020-06-18T09:11:03-03:00
author: "Matias, Ramirez"

draft: false

tags: ["github","ci/cd","automatizacion","blog"]
categories: ["github","ci/cd","automatizacion", "blog"]
featuredImage: "/img/los-simspons-hugo-travis.png"
lightgallery: true

---


Si algo caracteriza a muchos de los que trabajamos en sistemas es la aversión por hacer tareas repetitivas y monótonas y siempre que podamos vamos a recurrir a la automatización de esta manera nos queda mas tiempo para atender cosas importantes, aprender cosas nuevas, o simplemente el reto de automatizar esa tarea que tanto nos fastidia.
{{< youtube iqBfQF5bnbE >}}



### Como funciona este blog?

Primero comencemos por como funciona este blog, como comente [anteriormente](http://bhf.com.ar/creando-blog-con-hugo/) este blog esta creado con Hugo el cual mediante archivos Markdown genera archivos estáticos que pueden ser hosteados directamente en cualquier servidor web sin mas requisitos.
Para comenzar cree 2 repositorios de github, el primero es para mantener versionadas las entradas y configuración de este blog, el segundo es para hospedar mediante el servicio [GitHub Pages](https://pages.github.com/) y olvidarme de tener que mantener mi propio web server.

En un comienzo el proceso consistía en modificar o crear entradas, realizar manualmente en mi computadora el proceso de "build" mediante Hugo y enviar los archivos generados al segundo repositorio de github para ser visto mediante GitHub Pages. Este proceso resulta un poco engorroso ya que cada cambio necesita de resubir nuevamente los archivos modificados o nuevos.

### Automatizando el deploy mediante Travis

![Travis](/img/travis-githubpages.png "Travis CI y Github")

