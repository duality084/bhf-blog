---
title: "Bypasseando firewalls y proxys. Parte 1"
date: 2019-08-21T09:11:03-03:00
author: "Matias, Ramirez"

draft: true

tags: ["content", "Markdown"]
categories: ["selfhosted"]
featuredImage: "/img/youshallnotpass.jpeg"
lightgallery: true

---



Mas de una vez nos hemos encontrado en la situación en la que en el trabajo, un aeropuerto, un hotel, etc, la red es restrictiva o no tenemos "acceso" a internet, estas medidas se toman por los administradores de red por distintos motivos y se ayudan de distintas técnicas para lograrlo.

A continuación voy a hablar a grandes rasgos de algunas de las implementaciones para estos bloqueos, como saltearlos y posibles mitigaciones si nos toca estar del lado del administrador de la red
### 1 DNS

Primero una introduccion rapida a DNS
 	

>Pues bien, los DNS sirven para indicarle al usuario que teclea un dominio a que servidor debe ir a recoger la página web que desea consultar.
Efectivamente las páginas web realmente están hospedadas bajo una dirección IP, por ejemplo nuestra web www.digival.es realmente responde a la IP 85.112.29.231 pero este sistema es capaz de convertir estos números en el nombre de dominio www.digival.es. Recordar las IP de cada página web sería una trabajo demasiado duro, por eso se creó el sistema de nombres de dominio, para permitir crear términos y denominaciones más fáciles de recordar." [digival.es](https://www.digival.es/blog/que-son-las-dns-y-para-que-sirven/)

{{< figure src="/img/dns-server.png" width="100%" >}}


Ahora que sabemos que son los servidores DNS podemos ver que quizás nuestro querido administrador de red nos puede estar bloqueando la navegación a ciertos sitios debido que, a la hora de que nuestro navegador haga la petición por ejemplo a www.facebook.com este mismo sea redirigido a otra dirección IP en vez de donde se encuentran los servidores de Facbook, esto lo podemos verificar haciendo una consulta con herramientas como nslookup o dig para saber si realmente nuestras peticiones nos devuelve el servidor correcto. Teniendo en cuenta esto con solo cambiar nuestros servidores DNS en la configuración de nuestra tarjeta de red podríamos saltar el "bloqueo".

Para aquellos interesados en implementar un bloqueo de este tipo en su red tienen servicios como [OpenDns](https://www.opendns.com/) entre otros, en los cuales se pueden hacer una cuenta, configuran cuales son los sitios a los cuales no se les debería dar acceso, bajan el cliente en el cual se loguean con su cuenta correspondiente, seguidamente el cliente enviá tu IP publica a los servidores de OpenDns haciendo que todas las consultas DNS que provengan de tu IP a sus servidores sean respondidas según tus preferencias previamente configuradas, por ultimo mediante tu servidor DHCP o manualmente en cada dispositivo que querés que tenga el bloqueo configuras los servidores DNS para que apunte a los de OpenDNS. 

Si bien dijimos que el bloqueo puede ser salteado configurando manualmente nuestro servidor DNS manualmente en nuestra tarjeta de red, si nos encontramos en la posición de mitigar que los usuarios de nuestra red usen otros servidores DNS, lo que podemos hacer mediante el firewall de nuestro router es que todo el trafico que salga por el puerto 53(puerto del protocolo DNS) sea redirigido a los de OpenDNS, o que solo sea permitido el trafico por el puerto 53 a los servidores de DNS de nuestra preferencia, o alguna medida por el estilo.

Para finalizar hay que recordar que lo único que se bloquean son las consultas DNS, si queremos acceder a cualquier servicio al cual previamente conozcamos su IP vamos a poder hacerlo sin ningún inconveniente y de esta manera se abre un abanico de posibilidades para saltearnos este tipo de medida de las cuales vamos a hablar en las siguientes entradas.


