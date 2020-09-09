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


Si algo caracteriza a muchos de los que trabajamos en sistemas es la aversión por hacer tareas repetitivas y monótonas, siempre que podamos vamos a recurrir a la automatización, de esta manera nos queda mas tiempo para atender cosas importantes, aprender cosas nuevas o simplemente por el reto que significa automatizar esa tarea que tanto nos fastidia hacerla manualmente.
{{< youtube iqBfQF5bnbE >}}




## Como funciona este blog?

Primero comencemos por como funciona este blog, como comente [anteriormente](http://bhf.com.ar/creando-blog-con-hugo/) esta pagina esta creado con Hugo el cual mediante archivos Markdown genera archivos estáticos que pueden ser hosteados directamente en cualquier servidor web sin mas requisitos.
Para comenzar cree 2 repositorios de github, el primero es para mantener versionadas las entradas y configuración de este blog, el segundo es para hospedar mediante el servicio [GitHub Pages](https://pages.github.com/) y olvidarme de tener que mantener mi propio web server.

En un comienzo el proceso consistía en modificar o crear entradas, realizar manualmente en mi computadora el proceso de "build" mediante Hugo y enviar los archivos generados al segundo repositorio de github para ser visto mediante GitHub Pages. Este proceso resulta un poco engorroso ya que cada cambio necesita de resubir nuevamente los archivos modificados o nuevos.

## Automatizando el deploy mediante Travis

![Travis](/img/travis-githubpages.png "Travis CI y Github")

Mediante [Travis CI](https://travis-ci.org/) podemos automatizar el build y despliegue de nuestro sitio en GitHub Pages, para comenzar debemos crearnos un token para que Travis pueda acceder a nuestros repositorios, esto lo vamos a hacer mediante la url [https://github.com/settings/tokens/new](https://github.com/settings/tokens/new)

![TokenGithub](/images/travis-hugo/tokengithub.png "Asignamos los permisos.")

Una vez creado nuestro token lo guardamos para poder utilizarlo mas adelante, luego nos logueamos en [Travis CI](https://travis-ci.org/) con nuestra cuenta de github y habilitamos el repositorio donde estan los archivos de nuestro blog en basado en Hugo.

![Activar repositorio travis](/images/travis-hugo/travisenablerepo.png "Habilitamos el repositorio.")

En la configuración del repositorio agregamos la variable de entorno "GITHUB_TOKEN" con nuestro token generado anteriormente 

![Configuracion travis](/images/travis-hugo/travisconf.png "Configuramos nuestro repositorio en travis.")

Por ultimo necesitamos agregar el archivo .travis.yml a nuestro repositorio para que travis sepa como construir el sitio en mi caso con el siguiente contenido.

```yml
install:
  - curl -LO https://github.com/gohugoio/hugo/releases/download/v0.74.3/hugo_extended_0.74.3_Linux-64bit.deb
  - sudo dpkg -i hugo_extended_0.72.0_Linux-64bit.deb

script:
  - hugo # This commands builds your website on travis

deploy:
  local_dir: public # El directorio donde hugo va a generar los archivos estaticos
  repo: duality084/duality084.github.io # El repositorio donde se va a hacer el deploy
  target_branch: master # GitHub pages branch to deploy to (in other cases it can be gh-pages)
  provider: pages #El "provider" en este caso "pages" que nos va a dar las funcionalidades de "Github Pages" especificados mas adelante
  skip_cleanup: true
  github_token: $GITHUB_TOKEN # el token de autenticacion que pusimos como variable de entorno en el dashboard de travis.
  email: asd@asd.com
  name: "Ramirez Matias"
  fqdn: bhf.com.ar # Especificamos nuestro dominio si no vamos el asignado por github *.github.io, caso contrario borramos esta linea
  on:
    branch: master
```

Con esto ya tenemos todo armado para que Travis construya nuestro sitio con Hugo cada vez que hagamos un cambio en nuestro repositorio, ahora solo nos dedicamos a escribir sin tener que preocuparnos por nada mas.