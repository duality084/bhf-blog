---
title: "Escribiendo tu propia shellcode. Parte 1"
date: 2021-06-15T09:11:03-03:00
author: "Matias Ramirez"

draft: false

tags: ["x86","x64","ASM","Shellcode"]
categories: ["reversing","malware","ASM"]
featuredImage: "images/write-shellcode/portada.png"
lightgallery: true

---

## Introduccion

Escribir y entender como funciona una shellcode es un tema que tengo pendiente desde hace mucho tiempo y que mejor manera de intentarlo que con una serie de posts sobre el tema. Creo que el conocimiento sobre esta temática esta pendiente en varias personas, muchos de los que nos dedicamos a infosec en algun momento aprendimos como realizar el análisis de una vulnerabilidad del tipo buffer overflow, descubrimos donde falla la aplicación, como redirigir el flujo del programa, buscamos los "bad chars", pero al momento de escribir la shellcode usamos nuestro querido msfvenom o copiamos una del sitio "shell-storm" a modo de POC, esto no es casualidad desarrollar y entender como se escribe una shellcode requiere una serie de conocimientos previos algunos de los cuales ya los adquirimos mientras aprendíamos sobre BOF como por ejemplo, manejar un debugger, lenguaje ensamblador, como funciona el stack, etc. Pero con esto no alcanza, para que nuestra shellcode sea util necesitamos aprender un poco mas de lenguaje ensamblador y como interactuar con el sistema operativo subyacente para realizar tareas que nos beneficien, las instrucciones necesarias en ensamblador para realizar acciones en el sistema operativo cambian dependiendo del mismo y hay mas de una manera de hacerlo, no es lo mismo escribir una shellcode para Linux que para Windows.

## ¿Por que aprender escribir tu propia shellcode?

Que mejor que estar en una fiesta y contarles a los presentes que podes escribir una shellcode para ganar una shell inversa en 1 byte(bueno quizás estoy exagerando), vas a ser el alma de la fiesta y todos se van a arrodillar ante tal poder.

![Partyhard](/images/write-shellcode/almadelafiesta.png "Tienen que saberlo")

Haciendo los chistes a un lado siempre que podamos es bueno en nuestra profesión/hobby aprender que es lo que sucede tras bambalinas, hay que tener un equilibrio entre usar herramientas, crearlas o modificar las existentes uno mismo, ya que esto nos da una gran ventaja sobre quienes solo utilizan herramientas sin entender el por que suceden las cosas, si entejemos que y por que estamos haciendo lo que estamos haciendo vamos a poder llegar mas lejos y obtener mejores resultados. A modo de ejemplo, mas de una vez me encontré con una inyección SQL a la cual no le  podía sacar provecho utilizando SQLMap  y tuve que recurrir a enviar sentencias SQL a mano mediante BurpSuit o en casos mas complejos desarrollar mis propios scripts para demostrar el impacto de la vulnerabilidad, lo mismo sucede en este caso, tenemos una magnifica herramienta como lo es msfvenom para crear nuestras shellcodes pero a veces no funciona en cierto contexto y si conocemos de antemano todos los procesos que involucran la ejecución de una shellcode podemos intentar arreglar el problema, lo otro es que las herramientas automatizadas como SQLMap o msfvenom ya llevan bastante tiempo y su comportamiento esta mas que documentado haciendo que cualquier solución de seguridad detecte su accionar fácilmente, saber como funciona la herramienta para luego replicar o modificar levemente su comportamiento nos ayuda en la mayoría de los casos a saltear estas protecciones, creo que estos motivos son mas que suficientes para que le dediquemos un tiempo a aprender algo que siempre usamos pero que nunca nos preguntamos como funciona.

## ¿Que es una shellcode?

Una shellcode es un set de instrucciones diseñado para manipular la acciones que realiza un programa y que el mismo realice acciones que beneficien al atacante(copiar un archivo, leer un archivo, ejecutar comandos, obtener una reverse shell etc), para  que este set de instrucciones sea ejecutado la shellcode debe ser copiada a una region de memoria RAM y luego el flujo de ejecución del programa debe ser redirigido a esa sección de memoria para que las acciones se realicen


## ¿Como funciona una shellcode?

Como mencione al principio escribir una shellcode va a ser diferente dependiendo de cual sea el sistema operativo objetivo por como se maneja internamente las llamadas al sistema creo que es mas simple comenzar por como hacerlo en Linux

Para comenzar esa cadena de caracteres que siempre copiamos de nuestra salida de msfvenom no es mas que la representación en hexadecimal de código maquina, msfvenom nos da varias opciones para obtener la salida de la ejecucion del comando, podemos ejecutar lo siguiente para obtener un archivo binario para Linux equivalente a la ejecución del comando "echo 'Hola HTB'"

```
msfvenom -p linux/x86/exec CMD="echo 'Hola Htb'" -f elf > hola
```


![POC-msfvenom](/images/write-shellcode/hola-htb.png "Hola HTB")

Para obtener su equivalente en codigo maquina y analizar el resultado en hexadecimal y cadenas de texto podemos ejecutar lo siguiente:

```
msfvenom -p linux/x86/exec CMD="echo 'Hola Htb'" -f raw | xxd
```

![POC-msfvenom-hex](/images/write-shellcode/msfvenom-shellcode-hex.png "Shellcode hexadecimal")

Podemos observar algunas cadenas de texto existentes en el la salida del comando. "/bin/sh" escrito al revés(recordar que en la arquitectura x86 de Intel los bytes son representados en little endian), la cadena "-c" y por ultimo la cadena "echo 'Hola Htb'", solo analizando las cadenas de texto podemos inferir que lo que hace es ejecutar el comnando "/bin/sh" con los parámetros "-c echo 'Hola Htb'".

Por ultimo podemos obtener la representación en código ensamblador de la salida de msfvenom con el siguiente comando.

```
msfvenom -p linux/x86/exec CMD="echo 'Hola Htb'" -f raw | ndisasm -b32 -
```

![POC-msfvenom-hex](/images/write-shellcode/msfvenom-shellcode-disam.png "Shellcode hexadecimal")

Obtenemos el resultado en la "lengua oscura"(al menos para los que decidimos "programar" en bash) de la arquitectura X86, por mas que nos duela la vista, nos parezca incomprensible y nos intimide, aprender lenguaje ensamblador no tiene por que ser tan malo, desde mi punto de vista con que aprendamos que son y para que sirven los registros, que es el stack, algunas operaciones básicas del lenguaje (jmp,call,mov,push,pop) vamos a poder medianamente entender que es lo q sucede, tampoco hay necesidad de comerse toda la teoría primero y luego practicar por lo menos a mi me sirve practicar mientras aprendo y esto lo podemos lograr usando el debugger ejecutando instrucción por instrucción viendo como cambian los registros, el stack y por supuesto googlear todo aquello que no entendamos, luego por necesidad de realizar tareas mas complejos iremos profundizando  y aprendiendo mas sobre el lenguaje ensamblador.


## Syscalls en Linux

En los lenguajes de mas alto nivel por lo general usamos funciones integradas en el lenguaje que a su vez se traducen a llamadas a funciones propias del sistema operativo para realizar tareas como leer o escribir un archivo, mostrar un mensaje en pantalla, realizar una conexión TCP, etc. En ensamblador no tenemos estas funcionalidades integradas ergo no tenemos una función llamada "os.system" para ejecutar comandos, aun asi  podemos interactuar con el sistema operativo para realizar esas acciones, proceso un poco mas "tedioso" y con algunos detalles pero que cumple con nuestro cometido.

No hay una sola manera de hacerlo, tanto en Windows como en Linux podemos hacer una llamada a una librería que nos ayude en dicha tarea, por ejemplo podríamos usar "libc" en Linux y alguna de las funciones que exporta como "execv" o en Windows podríamos llamar a la función "CreateProcessA" de la librería "Kernel32.dll", pero esto se vuelve complejo en ensamblador ya que la librería debería estar cargada, si no lo esta cargarla y debemos buscar la dirección de la función que queremos llamar al momento de ejecutar nuestra shellcode para que funcione, no es que no se pueda pero es un ejercicio bastante mas complejo y extenso como para arrancar a armar nuestra primer shellcode. Para hacerlo mas fácil vamos a usar las "syscalls" o "llamadas al sistema" en Linux

### Interrupciones en lenguaje ensamblador

En lenguaje ensamblador tenemos una instrucción que se escribe como "INT" que provoca una interrupción por software, esta instrucción va precedida de un numero entre 0 y 255 en decimal, en nuestro caso vamos a usar la instrucción "INT" con el valor "80" en hexadecimal, esto provoca una interrupción que transfiere el flujo del programa al Kernel de Linux para realizar llamadas al sistema o "syscalls", para que el Kernel sepa que operación queremos realizar va a tomar el valor del registro "EAX".

### Desarrollando nuestra primer shellcode

Vamos a compilar un ejemplo de código en C que muestre un mensaje en pantalla.

```C
    #include <stdio.h>
    int main() {
    	printf("Hola mundo!\n");
    	return 0;
    }
```

Con el comando strace es posible ver que llamadas son realizadas por un programa, como vemos para imprimir un mensaje en por "stdout"(standard output) se utiliza la llamada "write"

![Hello-Strace](/images/write-shellcode/hello-strace.png "Ejecutando comando strace")

Para saber a que numero corresponde cada syscall podemos mirar el archivo "/usr/include/asm/unistd_32.h".

![syscall-write](/images/write-shellcode/syscall-write.png "Numeros de syscall")

Si queremos obtener mas informacion de "write" podemos ejecutar

```
man 2 write
```

![manwrite](/images/write-shellcode/man-write.png "Manualpage de write")

Recapitulando, entonces para poder crear una shellcode que sea capaz de mostrar un mensaje en pantalla debemos.

* Ejecutar la instrucción "Int 80h" para generar una llamada al sistema
* Para que el Kernel sepa que syscall vamos a ejecutar debemos escribir en el registro "EAX" un numero en nuestro caso para usar la llamada write tenemos que escribir el valor "4".
* Luego necesitamos pasarles los argumentos a la llamada, como el fd(file descriptor) en este caso el stdout que es equivalente a 1, el puntero a la dirección de memoria de nuestro mensaje, y por ultimo el tamaño del mensaje. Se espera que estos parámetros estén guardados en ciertos registros, "EBX" para el primero, "ECX" para el segundo y "EDX" para el tercero.

```
EAX => Valor 4 perteneciente a la syscall write
EBX => 1er argumento: fd o file descriptor. Es 1 para stdout.
ECX => 2do argumento: dirección de memoria del mensaje a imprimir.
EDX => 3er argumento: longitud del mensaje en bytes, serian 9 bytes para el mensaje 'Hola Htb' junto con el carácter de salto de linea .  
```

``` ASM
section .text
    global _start

_start:
mov eax,4 
; movemos el valor 4 a EAX para llamar a la syscall write
mov ebx,1 
; Movemos el valor 1 a EBX como primer parámetro para write
push 0x0a 
; hacemos un "push" del valor "0a" en hexadecimal equivalente al salto de linea al stack
push 0x62744820 
; Hacemos un push del valor de la cadena " Htb" representada en bytes en little endian
push 0x616c6f48 
; Hacemos un push del valor de la cadena "Hola" representada en bytes en little endian
mov ecx,esp 
; Movemos el valor de ESP, este valor contiene la dirección de memoria del tope de la "pila" o "stack" y 
;lo usamos como puntero para el string que queremos mostrar
mov edx,9 
; movemos el valor 9 a EDX, 9 es el valor de la longitud de la cadena que queremos imprimir en pantalla
int 80h 
; llamamos a la interrupción 80h

```

El código anterior no es el mejor y el mas apto para una shellcode luego vamos a ver por que, pero a fines prácticos de repasar en código ensamblador todo lo que nombramos anteriormente funciona.

Podemos guardar el código anterior y compilarlo de la siguiente manera.

```
nasm -f elf holahtb.asm 
ld -m elf_i386 -o holahtb holahtb.o 
```

Al principio cuesta mucho ver el código e intentar entender que es lo que va a suceder, créanme cuando les digo que a mi también me cuesta. Por este motivo podemos agarrar el desensamblador/debugger que mas nos guste y ver instrucción por instrucción que es lo q esta ocurriendo en la memoria y los registros.

En mi caso voy a usar IDA para verlo.

![IDA](/images/write-shellcode/ida-debugging.png "Analizando el codigo en IDA")

Podemos pararnos en la primera instrucción "mov eax,4" y poner un breakpoint con F2, iniciar el programa y cuando IDA se pare sobre esa sección del código ir avanzando instrucción por instrucción con F7.


![IDA](/images/write-shellcode/ida-view.png "Instrucciones, Registros, Stack")

1) Podemos ver nuestro set de instrucciones a ejecutar, IDA pauso la ejecución en la instrucción "MOV eax,4"
2) Vemos los valores de los registros
3) Los valores en el stack
4) La vista en hexadecimal y caracteres ASCII del stack

#### Paso  a paso
Como dije  podemos agarrar una hoja y un papel para hacer una traza la ejecución de las instrucciones paso a paso o agarramos un debugger para ver que esta sucediendo.

1) "MOV eax,4"

    Esta instrucción va a mover el valor 4 al registro EAX, esto es necesario para poder llamar a la syscall write para mostrar nuestro mensaje
    ![IDA](/images/write-shellcode/ida-debugging1.png "MOV eax,4")

2) "MOV ebx,1"

    Esta instrucción va a mover el valor 1 al registro EBX, esto es necesario para establecer a donde tiene que "escribir" el write, en nuestro caso a stdout
    ![IDA](/images/write-shellcode/ida-debugging2.png "MOV ebx,1")

3) "PUSH 0x0a"

    Esta instrucción va a mover el valor "0x0a" en hexadecimal al tope del stack, "0a" es el equivalente del salto de linea "\n" en hexadecimal
    ![IDA](/images/write-shellcode/ida-debugging3.png "PUSH 0x0a")

4) "PUSH 0x62744820"

    Esta instrucción va a mover el valor "0x62744820" en hexadecimal al tope del stack, "0x62744820" es el equivalente a " Htb"
    ![IDA](/images/write-shellcode/ida-debugging4.png "PUSH 0x62744820")

5) "PUSH 0x616C6F48"

    Esta instrucción va a mover el valor "0x616C6F48" en hexadecimal al tope del stack, "0x616C6F48" es el equivalente a "Hola"
    ![IDA](/images/write-shellcode/ida-debugging5.png "PUSH 0x616C6F48")

6) "MOV ecx,esp"

    Como vemos en la imagen ya tenemos nuestra cadena de texto "Hola Htb\n" cargada en el stack, para que se imprima en pantalla necesitamos que el valor de "ECX" apunte a donde esta alojada la cadena en memoria, para hacer eso movemos el valor de ESP que siempre apunta al tope del stack a ECX.
    ![IDA](/images/write-shellcode/ida-debugging6.png "MOV ecx,esp")

7) "MOV edx,9"

    Movemos el valor 9 a EDX para decirle a la syscall write que nuestra cadena a imprimir en pantalla tiene una longitud de 9 bytes.
    ![IDA](/images/write-shellcode/ida-debugging7.png "MOV edx,9")

8) "Int 0x80"

    Por ultimo se llama a la interrupcion 0x80 se le pasa el flujo de ejecución al Kernel y mediante la syscall imprime el mensaje deseado en base a los parametros que le fuimos pasando.
    ![IDA](/images/write-shellcode/ida-debugging7.png "Int 0x80")

![Hola-ASM](/images/write-shellcode/holaasm.png "Hola Htb en assembler")


Ya se lo que están pensando "8 largos pasos para mostrar un mensaje un mensaje en pantalla", y si por cosas como esta hoy tenemos lenguajes de mas alto nivel donde no nos tenemos que preocupar por tanto funcionamiento interno de un CPU, sistema operativo, memoria, etc.

Para finalizar podemos ejecutar lo siguiente para obtener la shellcode formateada:

```
printf '\\x' && objdump -d holahtb.o | grep "^ " | cut -f2 | tr -d ' ' | tr -d '\n' | sed 's/.\{2\}/&\\x /g'| head -c-3 | tr -d ' ' && echo ' '
```

Obtenemos el siguiente resultado:

```
\xb8\x04\x00\x00\x00\xbb\x01\x00\x00\x00\x6a\x0a\x68\x20\x48\x74\x62\x68\x48\x6f\x6c\x61\x89\xe1\xba\x09\x00\x00\x00\xcd\x80
```

Esta shellcode se puede mejorar mucho, se pueden evitar caracteres nulos y hacerla mas compacta, pero vamos a dejar eso para una próximo post.

Espero les haya gustado, cualquier consulta no duden en escribirme.

Happy Hacking.