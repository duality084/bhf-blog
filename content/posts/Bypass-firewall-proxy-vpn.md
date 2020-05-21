---
title: "Bypasseando firewalls y proxys. Parte 2"
date: 2019-10-03T09:11:03-03:00
draft: false
---



### 2 VPNs y Proxys


Como bien lo vimos en el articulo [anterior](https://blog.athtekun.com.ar/post/bypass-firewall-proxy/) puede que el administrador de red bloquee el acceso a sitios mediante las solicitudes que hace nuestra computadora a los servidores DNS, y como lo dije al final esta medida no es muy efectiva si el usuario es un poco mas experimentado o simplemente curioso, suponiendo que que al administrador tomo todas las medidas para que no usemos servidores DNS externos o distintos a los que el quiera aun sabiendo la IP podemos acceder al servidor que querramos.



{{< figure src="/img/tunnel-bypass.jpg">}}


Si el bloqueo es solo por DNS y se tomaron las medidas para que no usemos uno a nuestro antojo, no tendríamos problemas de acceder a un "servicio" en un servidor cuya ip conozcamos de antemano y mediante el mismo acceder de manera irrestricta a cualquier parte de internet, a continuación vamos a detallar brevemente alguno de los servicios que nos servirían para la tarea de acceder a internet mediante ellos.

1. **VPN**

>Empecemos por lo básico. VPN son las siglas de Virtual Private Network, o red privada virtual que, a diferencia de otras palabras informáticas más crípticas como DNS o HTTP, sí nos dan pistas bastante precisas sobre en qué consisten. La palabra clave aquí es virtual, pues es esta propiedad la que genera la necesidad de la VPN en sí, así como la que permite a las conexiones VPN ofrecerte los múltiples usos que veremos más adelante.
Para conectarse a Internet, tu móvil, PC, televisión y demás dispositivos generalmente se comunican con el router o módem que conecta tu casa con tu proveedor de Internet, ya sea mediante cable o inalámbricamente. Los componentes son distintos si estás usando la conexión de datos de tu móvil (que incluye su propio módem y habla con la antena de telefonía) pero la esencia es la misma: tu dispositivo se conecta a otro, que le conecta a Internet.
Lo más normal es que no tengas uno, sino varios dispositivos conectados al mismo router: móviles, ordenadores, consolas... En este caso cada uno tendrá asignada una dirección IP local, que no es visible desde Internet. Esto es una red local, un conjunto de dispositivos conectados de tal modo que puedan compartir archivos e impresoras sin necesidad de pasar por Internet.
Una conexión VPN lo que te permite es crear una red local sin necesidad que sus integrantes estén físicamente conectados entre sí, sino a través de Internet. Es el componente "virtual" del que hablábamos antes. Obtienes las ventajas de la red local (y alguna extra), con una mayor flexibilidad, pues la conexión es a través de Internet y puede por ejemplo ser de una punta del mundo a la otra. [XATAKA - ¿Qué es una conexión VPN, para qué sirve y qué ventajas tiene? ](https://www.xataka.com/basics/que-es-una-conexion-vpn-para-que-sirve-y-que-ventajas-tiene)

{{< figure src="/img/vpn-howworks.jpg">}}

2. **PROXYs**

Un servidor proxy es un software que permite la conexión entre 2 sistemas informáticos, generalmente el servidor proxy media entre una pc, celular, etc y internet, en una red que nos "bloquee" el trafico por DNS podemos usar un servidor proxy para poder navegar sin ninguna restricción ya que por lo general el encargado de resolver las solicitudes es el servidor proxy en si, también puede ser el encargado en una red corporativa o un "hotspot" en una cafetería, hotel, aeropuerto de bloquearnos la conexión, en la mayoria de los casos en las redes corporativas la pc que nos provee la empresa solo puede acceder a internet mediante un servidor proxy que es previamente configurado en nuestra pc de distintas maneras, en el caso de los hotspots se configuran de manera "transparente" redirigiendo desde el firewall todos nuestros intentos de conexión al proxy, de esta manera el administrador de la red se asegura que los unicos con acceso a internet sean aquellos autorizados previamente, también se puede hacer bloqueo de ciertos sitios o segmentar grupos de usuarios con ciertos permisos, horarios, obtención de métricas sobre el uso de la navegación, etc. Para finalizar nos podemos encontrar con servidores proxy Http/s o proxy socks.



Estos servicios se pueden conseguir gratis, pagos o montarlos uno mismo en un servidor en la nube o en tu misma casa siempre y cuando puedas abrir los puertos de tu conexión para usarlos, y nos pueden servir para saltear los bloqueos de una red aunque ahora requiera un poco mas de ingenio de nuestra parte.

Muchas veces podemos aprovechar malas configuraciones, olvidos o ciertas funciones que están habilitadas en una red para poder saltearnos el bloqueo. Por ejemplo me he topado con redes con un servidor proxy http en modo transparente donde no tengo usuario o mi usuario tiene limitaciones de navegación pero se permite todo el trafico saliente por el puerto 53(dns), de esta manera si el firewall no analiza el trafico que esta saliendo por el puerto 53 podemos montarnos o  usar un servidor VPN, PROXY, publico o de pago y saltearnos el bloqueo, si bien el ejemplo es con el puerto 53 puede ser cualquier otro solo es cuestión de probar. 


Si bien en esta post no se hablo mucho de como burlar un firewall o proxy y solo me centre en hablar de posibles herramientas que nos ayudarian en la tarea de hacerlo, en la próxima y ultima entrega de esta serie voy a contarles un poco mas de como y que maneras me he salteado la restricciones en redes "corporativas" 