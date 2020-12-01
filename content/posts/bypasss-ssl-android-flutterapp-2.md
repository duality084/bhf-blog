---
title: "Bypaseando la verificación SSL en una app de flutter en Android. Parte 2)"
date: 2020-11-25T09:11:03-03:00
author: "Matias Ramirez"

draft: false

tags: ["pentesting","mobile","flutter"]
categories: ["mobile","burpsuite"]
featuredImage: "images/bypasss-ssl-android-flutterapp/man-in-the-middle.png"
lightgallery: true

---

En la primera [parte](https://bhf.com.ar/bypasss-ssl-android-flutterapp/) vimos como encontrar el offset a parchear con frida, a partir de ahi solo deberíamos ejecutar el script de frida para que parchee la aplicacion que deseemos en memoria. Debido a que no deseaba rootear mi celular esto trajo aparejado otro desafiado. 


Para resumir lo que haremos es lo siguiente.

* Desempaquetar nuestro APK objetivo
* Descargar el [gadget](https://github.com/frida/frida/releases) de frida según la arquitectura de la aplicación objetivo arm64 o arm32.
* Inyectar el gadget en la aplicacion.
* Reempaquetar el APK.
* Subirlo e instalarlo a nuestro dispositivo.
* Configurar nuestro Linux como puerta de enlace.

Antes de arrancar cabe aclarar que necesitamos activar en nuestro dispositivo las "opciones de  desarrollador".


1) Descargamos y descomprimimos "frida-gadget".
```BASH
wget https://github.com/frida/frida/releases/download/14.1.0/frida-gadget-14.1.0-android-arm.so.xz
unxz -d frida-gadget-14.1.0-android-arm.so.xz
```

2) Desempaquetar el APK objetivo
```BASH
apktool d -rs target.apk
```
Se recomienda utilizar apktool para el desempaquetado ya que unzip/zip no siempre funciona correctamente.

3) Copiamos "frida-gadget" a la carpeta donde se encuentran los archivos desempaquetados.

```BASH
cp frida-gadget-14.1.0-android-arm.so target/lib/armeabi-v7a/libfrida-gadget.so
```
4) Escribimos un script en python para injectar "frida-gadget" usando la libreria [lief](https://github.com/lief-project/LIEF)(ver las instrucciones de instalacion en la pagina del proyecto)

```python
#!/usr/bin/env python3                                                                                                                                                                                                                     
                                                                                                                                                                                                                                           
import lief
                                                                                                                                                                                                                     
libnative = lief.parse("target/lib/armeabi-v7a/libflutter.so")                                                                                                                                                                            
libnative.add_library("libfrida-gadget.so") # Injection!                                                                                                                                                                                
libnative.write("target/lib/armeabi-v7a/libflutter.so")
```
Este script una vez ejecutado va a parchear "libflutter.so" y agrearle el gadget de frida como libreria.

5) Correr el script.
```bash
python3 inject-gadget.py
```
6) Reempaquetar el APK.
```bash
# "target" es el directorio donde desempaquetamos nuestro apk.
apktool b target

```
7) Firmar el APK, podemos usar ["Uber APK signer"](https://github.com/patrickfav/uber-apk-signer).
```bash
java -jar uber-apk-signer-1.2.1.jar -a ./target/dist/target.apk
```

8) Instalar el APK. Con nuestro dispositivo con las opciones de desarrollador habilitadas podemos usar adb para hacerlo.
```bash
adb install -r target/dist/target-aligned-debugSigned.apk
```

10) En nuestro dispositivo abrimos la aplicación instalada si la aplicación queda congelada es un buen indicio, si se cierra hay que revisar el proceso nuevamente ya que probablemente fallamos en algún paso.

11) Lo siguiente es correr el script de frida para que se "tracear" la aplicación y cada vez que se llame a la función encargaría de validar el certificado esta siempre devuelva que el resultado es correcto. 

El script es el siguiente:

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

Recordar cambiar la variable "pattern" que corresponde a los primeros bytes del inicio de la función según corresponda lo que hayamos encontrado en ghidra.

Con el script una vez guardado ejecutamos lo siguiente.(Recordar que debemos [instalar](https://frida.re/docs/frida-cli/) frida segun corresponda en Windows, MAC, Linux)

```bash
frida -U Gadget -l inject.js --no-pause
```

12) Por ultimo como las aplicaciones en flutter ignoran la configuración del proxy que que hayamos seteado en Android vamos a configurar nuestra maquina con Linux como puerta de enlace
```
sysctl -w net.ipv4.ip_forward=1
iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 80 -j REDIRECT --to-port 8080
iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 443 -j REDIRECT --to-port 8080
```

Con esto vamos a permitir el "fowarding" de paquetes y vamos a redirigir todo el trafico que llegue al puerto 443 y 80 al puerto 8080(cambiar la interfaz eth0 segun corresponda, por ejemplo si tienen su kali conectado por wifi en la notebook cambiar por wlan0, chequear cual es la interfaz correcta con el comando "ip a")

13) Cambiar la configuración de nuestro dispositivo con Android para usar nuestra maquina con Linux como puerta de enlace



A partir de aca ya vamos poder utilizar la herramienta que deseemos para analizar el trafico generado por nuestra aplicación.

Happy hacking!