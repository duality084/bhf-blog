---
title: "Bypaseando la verificación SSL en una app de flutter en Android. Parte 1)"
date: 2020-10-16T09:11:03-03:00
author: "Matias Ramirez"

draft: false

tags: ["pentesting","mobile","flutter"]
categories: ["mobile","burpsuite"]
featuredImage: "images/bypasss-ssl-android-flutterapp/man-in-the-middle.png"
lightgallery: true

---

Aun recuerdo cuando la vida era mas fácil para los chismosos y el SSL era utilizado con suerte solo en el envió del login, el resto de la información viajaba sin cifrar, mas o menos en 2010 eso empezó a cambiar luego de que saliera la extension de Firefox [Firesheep](https://www.dragonjar.org/firesheep-actualizado-para-capturar-las-cookies-sid-de-google.xhtml) cualquier persona con solo instalarse la extension podía realizar un ataque del tipo "man in the middle" robarse las cookies de sesión de sitios como Facebook, Twitter, Google y realizar un "session hijacking" o secuestro de sesión y sin saber la contraseña entrar en la cuenta del usuario afectado, junto con la expansión de las redes WIFI publicas esto era un gran problema,muchos se habian convertido en un potencial "hackerman".
![Hackerman](https://i.ytimg.com/vi/KEkrWRHCDQU/maxresdefault.jpg "Hackerman")

Por suerte las personas dedicadas a infosec están ahi al pie de cañon colaborando para que la seguridad de todas y todos mejore, el desarrollo de esa extension fue un tirón de oreja para que al menos los grandes como Google, Twitter, Facebook aplicaran SSL a todo el trafico web, luego mas tarde nació el proyecto "Let's encrypt" para ofrecer certificados SSL validos sin tener que poner nada de tu bolsillo, hoy no hay excusas mas que desidia del Sysadmin/desarrollador para no tener su sitio, api endpoint sin certificado SSL.

## Analisis de trafico en Android

Antes que nada quiero comentarles que no soy un experto en realizar análisis de trafico o reversing en en Android, lo he realizado de manera muy casual. Las veces anteriores que lo realice fue en versiones de Android 6 y anteriores, con la version 6 y anteriores realizar el análisis de trafico de una aplicación de Android es bastante trivial, abríamos nuestro burpsiuite lo poníamos a escuchar instalábamos el certificado de Burp en nuestro dispositivo, lo configurábamos para que el use el proxy que especifiquemos y listo. En Android 7 para adelante primero que nada las aplicaciones usan solo los certificados instalados en el sistema e ignoran los instalados por el usuario, ademas se hizo mas facil para los desarrolladores verificar que la conexión se realiza realmente con el servidor que esperan y no que en caso de un man in the middle con un certificado valido un atacante pueda interceptar el trafico, este chequeo se lo conoce como "SSL Pinning". Bueno para la seguridad de los usuarios de Android, malo para administradores de red de organizaciones en las cuales todo pasa por un proxy y un certificado autofirmado,para los investigadores/pentesters que deseen analizar el trafico no significa el fin pero si pone las cosas un poco mas complejas.
![SSL Everyhwere](images/bypasss-ssl-android-flutterapp/ssl-ssl-everywhere.jpg "SSL Everyhwere")

## Opciones

Para poder analizar el trafico en aplicaciones en Android 7 para adelante si podemos "rootear" el dispositivo vuelve a ser bastante trivial ya que tendríamos permisos de instalar cualquier certificado en el almacenamiento del "sistema", nuevamente configuramos nuestro proxy y esta todo funcionando. El problema es que con la facilidad que se le dio desarrolladores de Android de realizar un "SSL pining" muchas aplicaciones tienen esta verificación y tira por la borda lo anterior. Aquí entra en juego una maravillosa herramienta que nos da la capacidad de inyectar código en las aplicaciones y cambiar el comportamiento de la misma, estoy hablando de [frida](https://frida.re/). Frida es una herramienta que nos permite inyectar código en procesos en ejecución en múltiples plataformas, Android, iOS, Windows, MAC, etc. Si bien todo es mas fácil con un dispositivo rooteado en esta entrega lo voy a hacer en dispositivo sin dicha capacidad.


## Flutter

Flutter es un Framework de codigo abierto para el desarrollo de aplicaciones moviles desarrollado por Google. Flutter promete el desarrollo de aplicaciones nativas para Android e iOS de forma fácil, rápida y sencilla (no es eso lo que prometen todos los Frameworks(?)). En mi caso particular me encontré con que la aplicación sobre la cual quise realizar el análisis de trafico estaba hecha usando flutter, asi que sumado a lo que mencione anteriormente tuve que ponerme a buscar como funcionaba y me encontré con que las aplicaciones en Flutter ignoran completamente la configuración del proxy del sistema y cuando la aplicación se compila genera su propio "Keystore"  usando la libreria NSS de Mozilla, para resumir instalar un certificado como root en nuestro dispositivo no nos serviría ya que las aplicaciones desarrolladas en Flutter utilizan los certificados embebidos dentro de la aplicación al momento de compilarla.

## Reversing Flutter App
![Ingenieria inversa flutter APP](images/bypasss-ssl-android-flutterapp/reversee.jpg "Ingenieria inversa flutter APP")
Lo expuesto a continuación no fue desarrollo propio es producto de lo aprendido de este genial [post](https://blog.nviso.eu/2019/08/13/intercepting-traffic-from-android-flutter-applications/) escrito en ingles, no voy a profundizar mucho en el por que de lo que se replica a continuación asi que recomendada lectura si quieren profundizar mas sobre el tema.
Como nos comenta el autor del post que mencione gracias a que Dart el lenguaje de programación que usa Flutter es opensource y la librería BoringSSL también lo es, es "fácil" hacer un bypass de la validación del SSL, para ello debemos hookear la función encargarda de la verificación y para eso vamos a usar frida, para modificar el flujo de la función y hacer que siempre devuelva que verificación es correcta.
En el post se menciona que luego de hacer un análisis de las funciones en la librería BoringSSL encuentra una función  llamada "ssl_crypto_x509_session_verify_cert_chain" definida en [ssl_x509.cc](https://github.com/google/boringssl/blob/master/ssl/ssl_x509.cc#L362), esta funcion devuelve un tipo de dato booleano y la hace una buena candidata para realizar el hook sobre la misma. Para poder hookear la función con frida vamos a tener que saber cual es su dirección de memoria. Primero que nada vamos a tener que descargarnos el APK con la aplicacion en flutter que vamos a analizar, luego descomprimir sus archivos con la aplicación apktool(no se recomienda realizar un "unzip" ya que puede provocar un resultado no esperado). Una vez descargada podemos correr lo siguiente
```
apktool d -rs target.apk
```
Como mencionamos anteriormente Flutter genera aplicaciones nativas, asi que dentro de lo que descomprimimos podemos encontrar en la carpeta "lib" nuestra aplicación, en este caso 2 archivos en "libapp.so" donde se encuentra toda la lógica de aplicación y en "libflutter.so" todas las funcionalidades de flutter en si, como por ejemplo la función de la librería de BoringSSL que estamos buscando.
![Archivos Flutter](images/bypasss-ssl-android-flutterapp/lsflutterapp.png "Archivos Flutter")
Luego podemos proceder a decompilar "libflutter.so" con [ghidra](https://ghidra-sre.org/).

Para buscar la funcion "ssl_crypto_x509_session_verify_cert_chain" vamos a tener que decompilar "libflutter.so" y que ghidra haga un análisis, luego de que termine se nos menciona que mediante la funcion "Search -> Find Strings" en ghidra podemos buscar la cadena de texto "x509.cc"

![Busqueda Ghidra](images/bypasss-ssl-android-flutterapp/searchghidra.png "Busqueda Ghidra")

![Resultado Ghidra](images/bypasss-ssl-android-flutterapp/searchresultsghidra.png "Resultado Ghidra")

En nuestro caso tenemos 8 "lugares" donde aparece dicha cadena, como son pocas las coincidencias podemos hacer un analisis manual de cada una de las secciones en donde se encontro el string y ver si damos con algo que se parezca a la función "ssl_crypto_x509_session_verify_cert_chain".

![BoringSSL Funcion](images/bypasss-ssl-android-flutterapp/funcionssl.png "BoringSSL Funcion")

En el codigo en el repo de github podemos ver que la funcion toma 3 parametros y mas abajo que "OPENSSL_PUT_ERROR" es llamado una sola vez.

![BoringSSL Funcion](images/bypasss-ssl-android-flutterapp/funcionssl2.png "BoringSSL Funcion")

Luego de analizar manualmente las locaciones encontradas en ghidra damos con una funcion y su codigo decompilado en C que se asemejan a lo anterior.

![Funcion decompilada](images/bypasss-ssl-android-flutterapp/funciondecompilada.png "Funcion decompilada")


Perfecto encontramos nuestra funcion! Ahora ya podemos inyectar codigo con frida y modificar la función para que siempre devuelva "1" o verdadero y el chequeo ya no sea un problema. Para esto vamos a copiar los primeros 10 bytes de la funcion para generar una "firma", luego frida va a buscar esos primeros 10 bytes en la memoria del proceso y va a realizar el hook en la direccion que encuentre esos 10 bytes, recordar que tanto la direccion en la que encontramos la funcion como los primeros 10 bytes pueden cambiar por varios factores asi que recomiendo hacer el proceso por su cuenta y encontrar la direccion para su aplicacion particular, en mi caso es "2d e9 f0 4f a3 b0 81 46 50 20".


![Codigo ensamblador](images/bypasss-ssl-android-flutterapp/assemblercode.png "Seleccionamos los primeros 10 bytes")


Ya tenemos los bytes para encontrar la funcion, ahora nos falta el script encargado de realizar el hook, el cual es el siguiente

```JS
  function hook_ssl_verify_result(address)
{
  Interceptor.attach(address, {
    onEnter: function(args) {
      console.log("Disabling SSL validation")
    },
    onLeave: function(retval)
    {
      console.log("Retval: " + retval)
      retval.replace(0x1);

    }
  });
}
function disablePinning()
{
 var m = Process.findModuleByName("libflutter.so"); 
 var pattern = "2d e9 f0 4f a3 b0 81 46 50 20"
 var res = Memory.scan(m.base, m.size, pattern, {
  onMatch: function(address, size){
      console.log('[+] ssl_verify_result found at: ' + address.toString());

      // Add 0x01 because it's a THUMB function
      // Otherwise, we would get 'Error: unable to intercept function at 0x9906f8ac; please file a bug'
      hook_ssl_verify_result(address.add(0x01));
      
    }, 
  onError: function(reason){
      console.log('[!] There was an error scanning memory');
    },
    onComplete: function()
    {
      console.log("All done")
    }
  });
}
setTimeout(disablePinning, 1000)
```

## Conclusion

Por ahora terminamos con esta primer parte en la cual solo nos encargamos de encontrar la función que necesitamos para hacer el bypass, en la proxima parte vamos a re-empaquetar el APK con la aplicacion en Flutter y le vamos a agregar como dependencia el "gadget" de frida, de esta manera no es necesario que seamos root en el dispositivo para usar frida y realizar el hook, debido a que las aplicaciones el flutter hacen caso omiso a la configuración del proxy seteado en nuestro dispositivo vamos a tener setear a nuestra maquina con kali como puerta de enlace y redirigir todo el trafico hacia nuestro burpsuite con iptables.


Happy hacking!