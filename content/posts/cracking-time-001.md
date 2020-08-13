---
title: "Breve introducción al cracking - parte I"
subtitle: ""
date: 2020-08-10T00:28:29-03:00
lastmod: 2020-08-10T00:28:29-03:00
draft: false
author: "MemoryEmpty"
authorLink: ""
description: ""

tags: [cracking, encriptación, cesar]
categories: [cracking]

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: "images/cracking/keys.jpg"
featuredImagePreview: ""

toc:
  enable: true
math:
  enable: false
lightgallery: false
license: ""
---

# Breve introducción al cracking - parte I

> *No hay secreto que el tiempo no revele.* - Jean-Baptiste Racine


Habitualmente se cree que cuando hablamos de cracking, hablamos de una práctica que vulnera la privacidad ajena de las personas o de las empresas. Pero esto no siempre es así. Crackear no es una práctica ilegal *per se*, sino la puesta en práctica del conocimiento sobre las ciencias de la criptografía. Y digo ciencias, porque cuando hablamos de cifrar y descifrar información, no solo estamos hablando de computadoras, sino también de un amplio espectro de disciplinas que van desde la lingüística, la psicología, las matemáticas y, por supuesto, la informática. En resumen, crackear es una práctica, relacionada al estado del arte de una o varias ciencias, con el objetivo de obtener algún beneficio o alguna información, la cual en principio nos era negada.

![French_cypher_machine](images/cracking/French_cypher_machine.jpg#center "Imagen 1. Máquina de cifrado francesa del siglo XVI en forma de libro de Enrique II.")

Hoy en día, el cracking resulta un pilar importante en investigaciones. Por ejemplo, para hallar solución a los virus ransomwares, para atrapar redes criminales, para análisis forenses, en tácticas militares y, por supuesto, por hackers. Básicamente, siempre que alguien oculte información y alguien intente descifrarla estarán las condiciones para que podamos hablar de cracking. En cada caso, cada bando pondrá en marcha una serie de ingeniosos artilugios para lograr el objetivo deseado: ocultar u obtener información.

## 1. ¿Pero qué significa *"cifrar"*?

Cifrar refiere al proceso de alteración de la información cuyo resultado oculte las características de la materia en su estado inicial, con el fin de aumentar su seguridad. ¿De cuales características hablamos? Podemos mencionar algunas, como lo son características semánticas, sintácticas, lógicas, etc. Pero en realidad, la noción de característica que podemos alterar solo se limita a nuestra imaginación. La características de la información que alteremos pueden ser tantas las que se desean ocultar. Podría cifrarse un mensaje escrito y desarmar su estructura semántica o sintáctica, y sin embargo mantener los símbolos alfanuméricos, con lo cual el resultado ahora es ilegible, pero mantiene aún cierta relación con el mensaje original. Podría transformarse, por ejemplo, en una lista de caracteres ASCII, o en un mismo carácter repetido eternamente, lo que no necesariamente signifique que sea más seguro (pero no podemos negar que dichos casos nos parecen mas ingeniosos).

Ahora, cada vez que alteramos la información, ¿estamos cifrándola? ciertamente no, y la razón principal es que para que podamos hablar propiamente de un cifrado, se debe tener como punto de partida el objetivo original no pueda comprendido por personas ajenas al grupo de personas al que pertenece dicha información.

Por ejemplo, el código morse no pretende ocultar la información, solo la codifica de tal forma que -efectivamente- se han alterado las características del mensaje, pero a través de un método conocido y público, al cual cualquier persona podrá utilizar para obtener nuevamente el mensaje original. Si traducimos una palabra del español al sistema de kanji japonés, ciertamente será complicado de entender para alguien que no sepa leer kanjis, pero el mensaje, como tal, y el sistema para recuperarlo, no es ningún secreto, y cualquiera podrá acceder al mensaje si así lo desea. Para quienes tengan conocimientos en disciplinas humanísticas o sociales, pensarán en el polimorfismo de las diferentes lenguas, o de los códigos sociales y tribales. Estos, por más complejo que sea poder comprendernos, pertenecen al universo de los códigos y no de los cifrados.  

![morse](images/cracking/morse.jpg#center "Imagen 2. Telégrafo del siglo XIX")

Código y cifrado son fenomenológicamente parecidos, pero la principal diferencia está en que el código pretende ser interpretado y/o utilizado públicamente, no oculta nada, mientras que el cifrado tiene como único objetivo que la información permanezca **oculta**. El cifrado no pretende comunicar "per se": aunque el mensaje que haya sido cifrado puede tener un emisor y un destinatario, el cifrado no facilita la comunicación. De hecho es todo lo contrario, pretende transformar la información en algo secreto y/o incomprensible.

***¿Y que tiene que ver todo esto con cracking?***

Mucho, porque si cae en nuestras manos un mensaje, una cadena de texto extraña, un disco cifrado, un programa, o una grabación de la que sospechamos ha sido alterada, la primero que debemos preguntarnos es sobre su contexto. ¿Quien pudo haber enviado esto? ¿cómo pudo haberse alterado la información inicial? ¿Estará codificado o estará cifrado? Y si esta cifrado, seguramente nos preguntemos por si podemos identificar el sistema de encriptación, o ha sido alterado por un método creado particularmente para esa ocasión (esto no es raro, en algunos malwares encontramos este tipo de prácticas). También nos preguntaremos por la clave, si es que tiene una clave, y si es posible recuperarla o encontrarla por algún método, o si podemos saltearlos los métodos de autentificación de un programa, o alterarlos para nuestro beneficio, etc. Pero eso ya es motivo de otro capítulo. Lo importante y es lo que quiero dejar en claro, es que antes de ponernos a crackear cosas, lo mejor es intentar conocerlas y aprender de ellas. Es el camino más tedioso, pero a la larga el que más frutos y secretos nos revelará.

Hasta acá la introducción teórica. El título de la entrada dice cracking y aca en BHF no podemos irnos sin haber roto algo. Como suele pasar, en la antigüedad ya podemos encontrarnos casos ejemplares y que al día de hoy suele ser ejemplo de estudio. En el transcurso de esta tirada de cracking intentaremos avanzar por más teoría acompañada de estos ejemplos conocidos, más antiguos, más modernos, pero intentando que sean fáciles de asimilar.

## 2. El cifrado por desplazamiento o "el cifrado César"

![César](images/cracking/cesar.jpg#center)

Aunque el uso este tipo de cifrado puede hallarse en varias culturas, en la bibliografías es frecuente que se lo adjudiquen a Julio César, sobre todo por haber sido mencionado en varios textos de sus contemporáneos.

***¿En qué consiste el cifrado por desplazamiento?***

El cifrado por desplazamiento es un tipo de cifrado por sustitución. Como su nombre lo indica, se sustituye cada carácter inicial por otro a partir de n desplazamientos en el alfabeto. Así, si tomamos la clave `n = 3`, el resultado es que A pasa a ser D, D pasa a ser G, y así sucesivamente.

![César-crypt](images/cracking/cesar-example.png#center "Imagen 3. Mapa de cifrado por desplazamiento")


Veamos ahora una forma de representarlo en algún código. En principio, necesitamos una lista alfabética, para ello vamos a valernos de la librería *string*:

```
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

import string
list_alpha = list(string.ascii_lowercase)
list_alpha
len(list_alpha)


```
output:
>['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z']

>26

Con el comando `list()` ya obtenemos la lista de caracteres indexados, y también sabemos, a través del método `len()`, que sabemos que tiene un total de 26 valores. por lo que ahora basta con agregarle un valor N al valor index de cada elemento y así obtendremos su valor cifrado.

```
def CesarCipher(message, key):
    code = ""
    for letter in message:
            code += list_alpha[list_alpha.index(letter)+key]
    return code

CesarCipher("bhf", 2)

```

**output:** `djh`

Pues bien, el problema aquí es que si el valor de cada elemento + n es mayor a 25, se saldrá de la lista. Para simular la "vuelta al inicio" podemos restarle 26 (recuerden contar desde 0) a cualquier situación donde el valor obtenido sea >= 26:

```

def CesarCipher(message, key):
    code = ""
    for letter in message:
        number = int()
        number = list_alpha.index(letter)+key
        while number >= 26:
            number = number - 26
        code += list_alpha[number]
    return code

CesarCipher("bhf", 20)

```

**output:** `vbz`

Un último detalle: list_alpha no incluye los signos de puntuación, por lo que será prudente pasarlos por alto. El resultado final sería este:

```
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

import string
list_alpha = list(string.ascii_lowercase)

def CesarCipher(message, key):
    code = ""
    for letter in message:
        if letter in list_alpha:
            number = list_alpha.index(letter)+key
            while number >= 26:
                number -= 26
            code += list_alpha[number]
        else:
            code += letter
    return code

CesarCipher("ahora podemos usar el código de bhf para mandar mensajes cifrados.", 2)
```

**output:** `cjqtc rqfgoqu wuct gn eqfkiq fg djh rctc ocpfct ogpuclgu ekhtcfqu.`

> **nota:** Quizás quieran agregarle otro *condicional if* para las mayúsculas, simplemente transformar el mensaje a lowercase.


***Muy bien, ya tenemos nuestro mensaje. ¿Ahora, como podemos crackearlo?***

Supongo que recuerdan que en la introducción hablaba que antes de ponernos a crackear conviene hacerse algunas preguntas. Si nosotros sabemos que el mensaje se cifró por desplazamiento, pues podemos continuar desplazándose con el mismo código para obtener como resultado la posición inicial. Si se utilizó key = 2, entonces 26 - 2 = 24. Veámoslo en marcha:

```
CesarCipher("gp gn ekhtcfq eguct, cn eqorngvct gn dweng fg fgurncbcokgpvq, qdvgpgoqu gn ogpuclg qtkikpcn.", 24)
```
output:
`en el cifrado cesar, al completar el bucle de desplazamiento, obtenemos el mensaje original.`

Bueno. Supongamos que no sabemos que la key es igual a 2. Podemos simplemente pasarlo por un `bucle for` de 26 intentos, para ver si alguno nos resultará familiar:

```
message = "kt kyzk igyu kr sktygpk ky jkiahokxzu vux hayigx rg igjktg hnl kt zujuy ruy xkyarzgjuy uhzktojuy."

for n in range(26):
    CesarCipher(message, n)
```

podrán imaginarse que el output no es otra cosa que 26 líneas, y una sola es la correcta. Este tipo de ataque entra dentro de la categoría llamada *"fuerza bruta"* (pero no crean que fuerza bruta es solo esto). En este caso el exceso de falsos positivos no es grave, pero si queremos aplicar esta técnica en un código con más posibilidades, vamos a necesitar filtrar el resultado correcto. Ahí es donde entra la noción que llamaremos menú, aunque también puede que lo encuentren como wordlist u otras varias formas más.

> En cracking, un **menú** es una cadena, una palabra, o un conjunto de ellas, que esperamos encontrar en el mensaje original, y que por lo tanto nos pueden servir para identificar el resultado positivo.

Pensar un menú puede ser una tarea difícil si nos basamos únicamente en la programación, ya que en sí no hay razones para que una computadora diferencie entre una cadena sea o no parte del cuerpo de palabras de una lengua (aunque claro que podemos aplicar técnicas para que eso ocurra, pero eso ya es otra historia), sin embargo, pensarlo desde el factor humano, en ocasiones, puede ahorrarnos millones de intentos. En nuestro ejemplo, podríamos pensar como menú la palabra cadena "bhf", o los miembros del grupo, o algunas fechas conocidas. Veamos un ejemplo:

```
message = "kt kyzk igyu kr sktygpk ky jkiahokxzu vux hayigx rg igjktg hnl kt zujuy ruy xkyarzgjuy uhzktojuy."

for n in range(26):
    res = CesarCipher(message, n)
    if "bhf" in res:
        print(res)

```

**output:** `en este caso el mensaje es decubierto por buscar la cadena bhf en todos los resultados obtenidos.`

> **tip:** En casos parecidos, en que no sabemos nada respecto al mensaje, podemos usar una lista de palabras de uso frecuente, por ejemplo ['para', 'ella', 'programa', 'dinero','etc',]

Hasta aquí hemos repasado conceptos como básicos como lo son criptografía, cracking, código, encriptación y menú o wordlist. Por curioso que resulte, estas técnicas básicas las aplicaremos con frecuencia. Muchos de los programas conocidos como *hashcat, John The Ripper, aircrack*, aplican esta misma metodología, claro que en escenarios mas complejos.

Espero que les haya gustado, y que les ha parecido sencillo, o al menos no tan complejo. Más adelante veremos como estas técnicas pueden combinarse con muchas otras, y también algunas formas para defendernos de ellas.

**Happy cracking!**

Referencias:

* Al Sweigart, Cracking Codes with Python: An Introduction to Building and Breaking Ciphers.
* https://es.wikipedia.org/wiki/Cifrado_(criptograf%C3%ADa)
* [cesar]https://www.rauljaimemaestre.com/the-mathematics-behind-blockchain-parte-iii-tipos-de-cifrado-y-las-funciones-hash/
* Las imagenes no referenciadas pertenecen al banco de imagenes de https://commons.wikimedia.org y su información se halla los metadatos de la misma.
