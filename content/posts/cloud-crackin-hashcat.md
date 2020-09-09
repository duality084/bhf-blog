---
title: "Crackeando contraseñas en la 'Nube' y GRATIS!"
date: 2020-09-09T09:09:03-03:00
author: "Matias, Ramirez"

draft: false

tags: ["cracking","cloud","ctf"]
categories: ["ctf","cracking","cloud"]
featuredImage: "/images/cloud-cracking-hashcat/crack-rig.jpg"
lightgallery: true

---

Bienaventurados aquellos que posean el poder adquisitivo de comprarse la bestialidad que vemos en la imagen. Para aquellos que no se hayan dado cuenta en la imagen podemos observar un "rig de cracking" que cuenta con 9 placas de video Nvidia GeForce RTX 2080, la imagen la encontré en un posteo de  un usuario de [Twitter](https://twitter.com/mrb3n813/status/1222579448215633921)

## ¿Por que?
Primero comencemos en por que alguien querría gastar tanto dinero en semejante equipo, aca no solo hay que contar el costo de las placas de video que cuestan de 750 a 850 USD cada una, las fuentes de alimentación para darles energía, el gabinete capaz de albergarlas a todas, una placa madre con soporte para esa cantidad de placas, CPU, RAM. Estamos hablando de unos 8 a 9 mil USD, si esto lo queremos llevar a Argentina el precio es aun mayor.



Por que alguien querría realizar semejante inversión? La utilización del poder de computo que brinda una placa de video o varias funcionando en conjunto no queda solo relegado al mundo de los video juegos, ya sabemos que se usa en otros ámbitos como minar criptomonedas, inteligencia artificial y como no también en el mundo de seguridad informatica.

Comencemos dejando de hablar de "placas de videos" y pasemos a uno de sus componentes el GPU, el GPU a diferencia del CPU esta diseñado con un propósito especifico, compuestos de cientos o miles de nucleos "sencillos" para procesar grandes cantidades de datos y realizar las mismas operaciones una y otra vez, mientras que el CPU es de proposito "general", compuesto por pocos nucleos "complejos" y esta diseñado para realizar cualquier tipo de calculo. Aprovechando la capacidad de paralelismo del GPU podemos pedirle que realice cálculos específicos a una gran velocidad. Les dejo un video para que se entiendan las diferencias.

{{< youtube -P28LKWTzrI >}}

Para nuestro caso y nuestros intereses podemos usar la capacidad de una o varias GPUs para hacer fuerza bruta a los hashes de contraseñas que hayamos obtenido y conocer el valor de la contraseña.

> ¿Que es una funcion hash?
>>Una función criptográfica hash- usualmente conocida como “hash”- es un algoritmo matemático que transforma cualquier bloque arbitrario de datos en una nueva serie de caracteres con una longitud fija. Independientemente de la longitud de los datos de entrada, el valor hash de salida tendrá siempre la misma longitud. [Kapersky Latam Blog](https://latam.kaspersky.com/blog/que-es-un-hash-y-como-funciona/2806/)

## ¿Como?
Usar capacidad de computo para romper contraseñas no es nada nuevo, pero últimamente con las mejores capacidades de los CPUs y por sobre todo GPUs las contraseñas poco complejas son vulneradas en cuestión de segundos.

Herramientas para hacer fuerza bruta hay muchas, quizás la mas conocida y mas utilizada de todas es ["John the ripper"](https://github.com/openwall/john), hoy en dia tenemos mejores herramientas sobre todo para aprovechar la capacidad de paralelismo del GPU, en este caso vamos a estar utilizando y hablando de [hashcat](https://hashcat.net/hashcat/).

Hashcat a diferencia de otras herramientas puede hacer uso del GPU para realizar fuerza bruta. Soporta cientos de funciones hash y las cientos de variantes que se conocen, provee la capacidad de usar un diccionario predefinido, o armar mutaciones de conjuntos de caracteres, esta entrada no va a ser extensiva con el uso de la herramienta y recomiendo dirigirse a su sitio para conocer mejor sus funcionalidades.

## PERO COMO!!???

Ya sabemos que de este lado del mundo poder acceder a esa capacidad de computo esta muy lejos de nuestros bolsillos, incluso solo comprarse una placa de video puede ser bastante costoso.
![Money!](https://media1.tenor.com/images/8bfcd31a36c9755326622397f2cca856/tenor.gif "Money")

Nnuca esta de mas contar con la capacidad de romper hashes, ya sea por conocer esa contraseña de esa red de Fibertel, mientras hacemos un CTF o realizamos un pentest, pero como dijimos lo mas probable es que solo contemos con aquella vieja Nvidia 9800 o simplemente con nuestra notebook y su i3 corriendo con 4 maquinas virtuales al mismo tiempo.

## Google Colab

Viendo videos de [John Hammond](https://www.youtube.com/channel/UCVeW9qkBjo3zosnqUbG7CFw) llegue a "Google Colaboratory" la herramienta de Google para ejecutar codigo de Python con el acceso a GPUs, Y GRATIS!, esto esta hecho para que estudiantes, cientificos de datos e investigadores en inteligencia artificial tengan acceso al uso de GPUs para sus calculos.

Si bien esta pensando para ejecutar codigo de python directamente también podemos ejecutar comandos en la instancia que se nos provee e instalar software. Lo que vamos a hacer es lo siguiente:

* Instalar y configurar un servidor ssh
* Instalar y configurar ngrok para realizar un tunel tcp al puerto 22 y conectarnos por ssh
* Compilar e instalar hashcat
* Profit!

Podemos acceder a Colab con nuestra cuenta de google, vamos a utilizar el siguiente [enlace](https://colab.research.google.com/github/duality084/colab-hashcat-ssh/blob/master/SSH-hashcat.ipynb) que ya trae todo los pasos anteriormente descritos.

![Colab](/images/cloud-cracking-hashcat/colab-hashcat.png "Colab")

Mediante ese enlace se va a acceder a Colab, se va a tomar el archivo SSH-hashcat.ipynb de mi repo de Github y se va cargar, antes de comenzar vamos a necesitar 2 cosas una cuenta en [ngrok](https://dashboard.ngrok.com/signup) y generar una "ssh key" para conectarnos a nuestra instancia por ssh.

Para generar una "ssh key" podemos en Linux ejecutar el comando "ssh-keygen" de la siguiente forma:
```
ssh-keygen -f colab
```
![Ssh-keygen](/images/cloud-cracking-hashcat/ssh-keygen.png "ssh-keygen")

Esto nos va a crear la clave publica y privada en esta parte vamos a necesitar la clave publica que la podemos ver la de siguiente manera:
```
cat colab.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDcEQehlls3I58AvJB4H0Dhqvkt7OCfEFRXEH0dYIaUGqc5Ox+EM1CaywEEhbWSxbdwnaSuaVvPx9DYEMiRdDfqA0ER4y3YcvRQhsMQj/6FrBg7LZrMDrt6/MJ82F9dia8p0KkWtEqsAdbTFK1nY9ICayi/MHy4nH+KX+2hFo0egcuP1nUOD3TsaF1u1lCWiOvqtaieGQpPPoupaUIYip1EdQRcN/0IPmEBaN9oInypim1E2qX6pWe6DQwUaT0sSu3jlyGuGOxq6MxlfPhvI7nDjs6CT7eaLFFYUTuSH7fnfS5zP9E+kVae0TJ16/qo5igm5ARPhnlb6db3hZ64qmup root@8ae1bd63e6e1
```

Procedemos a ejectuar cada una de las instrucciones del archivo .ipynb en secuencia
1) Esto nos va a instalar ngrok y el servidor ssh
```
! wget https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-linux-amd64.zip > /dev/null
! unzip ngrok-stable-linux-amd64.zip > /dev/null 
! apt-get install openssh-server
```
2) Esto nos va a configurar y dejar corriendo ngrok, en esta parte nos va a pedir que ingresemos el token de ngrok el cual se consiguie accediendo a https://dashboard.ngrok.com/authuna vez tenemos una cuenta
```
print("Copy authtoken from https://dashboard.ngrok.com/auth")
import getpass
authtoken = getpass.getpass()
get_ipython().system_raw('./ngrok authtoken $authtoken && ./ngrok tcp 22 &')
```

3) Configura el servidor ssh y lo deja corriendo, en esta parte se nos va a pedir la clave publica previamente generada.
```
! echo "PubkeyAuthentication yes" >> /etc/ssh/sshd_config
! echo "PermitRootLogin prohibit-password" >> /etc/ssh/sshd_config
! echo "AuthorizedKeysFile	.ssh/authorized_keys" >> /etc/ssh/sshd_config
! echo "LD_LIBRARY_PATH=/usr/lib64-nvidia" >> /root/.bashrc
! echo "export LD_LIBRARY_PATH" >> /root/.bashrc
! mkdir -p /var/run/sshd
! mkdir -p /root/.ssh
! touch /root/.ssh/authorized_keys
! ls -lah /root/.ssh
import getpass
print("Paste your SSH public key: ")
publickey = getpass.getpass()
get_ipython().system_raw('echo $publickey  >> /root/.ssh/authorized_keys')
get_ipython().system_raw('/usr/sbin/sshd -D &')
```

4) Instala hashcat
```
!apt install cmake build-essential -y
!apt install checkinstall git -y
!git clone https://github.com/hashcat/hashcat.git
!cd hashcat && git submodule update --init && make && make install
```

5) Nos muestra con que puerto y nombre de dominio podemos acceder al servicio ssh.
```
! curl -s http://localhost:4040/api/tunnels | python3 -c "import sys, json; print(json.load(sys.stdin)['tunnels'][0]['public_url'])"
```

Al ejecutar este ultimo paso vamos a obtener el nombre de dominio y el puerto por el cual nos podemos conectar a nuestra instancia por SSH.

![Exito](/images/cloud-cracking-hashcat/ngrok-success.png "Exito!")

Nos conectamos en nuestra maquina con Linux utilizando el comando ssh, especificando nuestra clave privada con el flag -i y con el flag -p el puerto. Ej:
```
ssh -i colab -p 10448 root@0.tcp.ngrok.io
```
A continuacion podemos ver que nos encontramos con una GPU Nvidia Tesla T4

![Tesla T4](/images/cloud-cracking-hashcat/nvidia-smi.png "Tesla T4")

No podemos elegir que GPU utilizar pero cuando inicalizamos nuestra instancia nos pueden tocar las siguientes

* Nvidia Tesla K80
* Nvidia Tesla T4
* Nvidia Tesla P4
* Nvidia Tesla P100

Realizamos una prueba de rendimiento con hashcat, con el tipo de hash que nos podemos encontrar en las redes Wifi protegidas con WPA.

![Benchmark Colab](/images/cloud-cracking-hashcat/benchmark-colab.png "Benchmark Colab")

La misma prueba en mi GPU una Nvidia 1070 arroja los siguientes resultados

![Benchmark 1070](/images/cloud-cracking-hashcat/benchmark-1070.png "Benchmark 1070")

Podemos ver que en Colab el resultado es por poco superior, con capacidad de realizar 357000 hashes por segundo. Mientras que la 1070 es de 342000, en otras pruebas me toco el GPU Tesla P4 y el resultado era 416000 hashes por segundo.

Cabe aclarar que el performance puede no ser estable, hay que recordar que los recursos son compartidos con otras personas que utilizan la plataforma. Pero como dice el dicho "A caballo regalado no se le miran los dientes"

## Conclusion

Luego de estos pasos podemos utilizar Colab, y hashcat para seguramente por lejos obtener mejor rendimiento que en nuestro Kali virtualizado con un solo core asignado. Si bien mostre como conectarme por ssh a la instancia que nos provee google, los comandos tambien pueden ser ejecutados directamente desde la interfaz de Colab.
![Ejecucion comandos](/images/cloud-cracking-hashcat/ejecucion-comandos-colab.png "Ejecucion comandos")

Por ultimo les dejo los 2 repositorios de los cuales hice el archivo que comparti en mi repo, sacando ideas de los 2 y que se ajuste a mis necesidades-
* [https://github.com/someshkar/colabcat](https://github.com/someshkar/colabcat)
* [https://github.com/semihucann/hash_cracking_with_gpu](https://github.com/semihucann/hash_cracking_with_gpu)

Happy Cracking!

