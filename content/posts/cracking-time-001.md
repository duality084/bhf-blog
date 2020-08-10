---
title: "Breve introducción al cracking - parte I"
subtitle: ""
date: 2020-08-10T00:28:29-03:00
lastmod: 2020-08-10T00:28:29-03:00
draft: false
author: ""
authorLink: ""
description: ""

tags: []
categories: []

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: ""
featuredImagePreview: ""

toc:
  enable: true
math:
  enable: false
lightgallery: false
license: ""
---

# Breve introducción al cracking - parte I

> Si existen secretos, habrá oídos que quieran conocerlos.

Podríamos describir al **cracking** como el arte de descifrar o reconstruir lo que se oculta detrás de un mensaje que ha sido deliberadamente cifrado para, precisamente, que nadie acceda a su información.

Habitualmente se cree que cuando hablamos de cracking, hablamos de una práctica que vulneran la privacidad ajena de las personas o de las empresas. Pero esto siempre no es así. Más allá del cuerpo de leyes que rigen sobre un territorio, crackear no es sinónimo de ilegalidad, sino la puesta en práctica del conocimiento sobre las ciencias de la criptología. Y digo ciencias, porque cuando hablamos de cifrar y descifrar información, no solo estamos hablando de computadoras, sino también de un amplio espectro de disciplinas que van desde la lingüística, la psicología, las matemáticas, (y más). En resumen, crackear es una práctica, relacionado al estado del arte de una o varias ciencias, relacionadas a descifrar mensajes ocultos.

Hoy en día el cracking resulta un pilar importante en investigaciones, por ejemplo para hallar solución a los famosos virus ransomwares, para atrapar redes criminales, para análisis forenses, en tácticas militares, y, por supuesto, por hackers. Básicamente, siempre que alguien oculte información y alguien intente descifrarla, estarán las condiciones para que podamos hablar de cracking. En cada caso, cada bando pondrá en marcha una serie de ingeniosos artilugios para lograr el objetivo deseado: Ocultar u obtener información.

## 1. ¿Pero qué significa *"cifrar"*?

Cifrar refiere al proceso de alteración de la información cuyo resultado oculte las características de la materia en su estado inicial. ¿De cuales características hablamos? En realidad, podemos mencionar algunas como lo son caracteristicas semanticas, audioperceptivas, logicas, etc. Pero en realidad, la nocion de caracteristica que podemos alterar solo se limita a nuestra imaginación. La características alteradas pueden ser tantas las que se desean ocultar. Podría cifrarse un mensaje escrito y desarmar su estructura semántica o sintáctica, y sin embargo mantener los simbolos alfanumericos, con lo cual el resultado ahora es ilegible, pero mantiene aún cierta relación con el mensaje original. Podría transformarse, por ejemplo, un sonido en una imagen, o un video en caracteres hexadecimales, y en estos casos las propiedades de las caracteristicas iniciales se alejan aún más de su origen, lo que no necesariamente signifique que sea más seguro (pero no podemos negar que dichos casos nos parecen mas ingeniosos).

Ahora, cada vez que alteramos un mensaje, ¿estamos cifrando? ciertamente no, y la razón principal es que para que podamos hablar propiamente de un cifrado, se debe tener como punto de partida el objetivo de que el mensaje original no pueda comprendido por personas ajenas al grupo de personas (o programas) destinatarias. 

Por ejemplo, el código morse no pretende ocultar la información, solo la codifica de tal forma que -efectivamente- se han alterado las características del mensaje, pero a través de un método conocido y público, al cual cualquier persona podrá utilizar para obtener nuevamente el mensaje original. Si traducimos una palabra del español al sistema de kanji japonés, ciertamente deja de ser accesible para alguien que no sepa leer kanjis, pero el mensaje, como tal, y el sistema para recuperarlo, no es ningun secreto, y cualquiera podrá acceder al mensaje si así lo desea. Para quienes tengan conocimientos en disciplinas humanísticas o sociales, ...


código y cifrado son fenomenológicamente parecidos, pero la principal diferencia está en que el código pretende ser poder ser reconstruido por cualquiera que conozca el código, no oculta nada, mientras que el cifrado tiene como único objetivo que el mensaje permanezca **oculto**.

*¿Y que tiene que ver todo esto con cracking?*

¡Todo! Porque si cae en nuestras manos un mensaje, una cadena de texto extraña, un disco cifrado, una sonido con características sospechosas, la primero que debemos preguntarnos es sobre su contexto. ¿Quien pudo haber enviado esto? ¿cómo pudo haber alterado la información inicial? ¿Habrá utilizado un código, o está encriptado? Y si esta encriptado, seguramente nos preguntemos por si podemos identificar el sistema de encriptación, o ha sido alterado por un método creado particularmente para ese mensaje (esto no es raro, en algunos malwares encontramos este tipo de métodos custom). También nos preguntaremos por la clave, (si es que tiene una clave), y si es posible recuperarla o encontrarla por algún método, pero eso ya es motivo de otro capítulo. Lo importante y es lo que quiero dejar en claro, es que antes de ponernos a crackear cosas, lo mejor es intentar conocerlas y aprender de ellas. Es el camino más tedioso, pero a la larga el que más frutos y secretos nos revelará.

Hasta acá la introducción. El título de la entrada dice cracking y aca en BHF no podemos irnos sin haber roto algo. Como suele pasar, en la antiguedad ya encontramos casos ejemplares y que al día de hoy suele ser ejemplo de estudio. En el transcurso de esta tirada de cracking intentaremos avanzar por más teoría acompañada de estos ejemplos conocidos, más antiguos, más modernos, pero intentando que sean fáciles de asimilar.

## 2. El cifrado por desplazamiento o "el cifrado César"

Aunque el uso este tipo de cifrado puede hallarse en varias culturas, en la bibliografías es frecuente que se lo adjudiquen a Julio César, sobre todo por haber sido mencionado en varios textos de sus contemporáneos.

*¿En qué consiste el cifrado por desplazamiento?*

El cifrado por desplazamiento es un tipo de cifrado por sustitución. Como su nombre lo indica, se sustituye cada carácter inicial por otro a partir de n desplazamientos en el alfabeto. Así, si tomamos la clave `n = 2`, el resultado es que a pasa a ser c, d pasa a ser f, y así sucesivamente.

IMAGEN

veamos ahora una forma de representarlo en algún código. En principio, necesitamos una lista alfabética, para ello vamos a valernos de la librería *string*:

```
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

import string
list_alpha = list(string.ascii_lowercase)
list_alpha
len(lista_alpha)

output:
['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z']
26
```

Con el comando `list()` ya obtenemos la lista de caracteres indexados, y también sabemos (a traves del metodo `len()`) que sabemos que tiene 26 valores. por lo que ahora basta con agregarle un valor n al valor index de cada elemento y así obtendremos su valor cifrado.

```
def Cesar Crypter(message, key):
    code = ""
    for letter in message:
            code += list_alpha[list_alpha.index(letter)+key]
    return code

CesarCrypter("bhf", 2)

>output:
>djh
```

Pues bien, el problema aquí es que si el valor de cada elemento + n es mayor a 25, se saldrá de la lista. Para simular la "vuelta al inicio" podemos restarle 26 (recuerden contar desde 0) a cualquier situación donde el valor obtenido sea >= 26:

```

def Cesar Crypter(message, key):
    code = ""
    for letter in message:
        number = int()
        number = list_alpha.index(letter)+key
        if number >= 26:
            number = number - 26
        code += list_alpha[number]
    return code

Cesar Crypter("bhf", 20)

>output:
>vbz
```

Un último detalle: list_alpha no incluye los signos de puntuación, por lo que será prudente pasarlos por alto. El resultado final sería este:

```
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

import string
list_alpha = list(string.ascii_lowercase)

def Cesar Crypter(message, key):
    code = ""
    for letter in message:
        if letter in list_alpha:
            number = list_alpha.index(letter)+key
            if number >= 26:
                number -= 26
            code += list_alpha[number]
        else:
            code += letter   
    return code

CesarCrypther("ahora podemos usar el código de bhf para mandar mensajes cifrados.", 2)
>output
>cjqtc rqfgoqu wuct gn eqfkiq fg djh rctc ocpfct ogpuclgu ekhtcfqu.
```

>nota: Quizás quieran agregarle otro `condicional if` para las mayúsculas, simplemente transformar el mensaje a lowercase.

Muy bien, ya tenemos nuestro mensaje. *¿Ahora, como podemos crackearlo?*

Supongo que recuerdan toda la introducción que hablaba que antes de ponernos a crackear conviene hacerse algunas preguntas. Si nosotros sabemos que el mensaje se cifró por desplazamiento, pues podemos continuar desplazándose con el mismo código para obtener como resultado la posición inicial. Si se utilizó key = 2, entonces 26 - 2 = 24. Veámoslo en marcha:

```
Cesar Crypter("cjqtc rqfgoqu wuct gn eqfkiq fg djh rctc ocpfct ogpuclgu ekhtcfqu.", 24)
ahora podemos usar el código de bhf para mandar mensajes cifrados.
```

Bueno. Supongamos que no sabemos que la key es igual a 2. Podemos simplemente pasarlo por un `bucle for` de 26 intentos, para ver si alguno nos resultará familiar:

```
message = "cjqtc rqfgoqu wuct gn eqfkiq fg djh rctc ocpfct ogpuclgu ekhtcfqu."

for n in range(26):
    Cesar Crypter(message, n)
```

podrán imaginarse que el output no es otra cosa que 26 líneas, y una sola es la correcta. Este tipo de ataque entra dentro de la categoría llamada *"fuerza bruta"* (pero no crean que fuerza bruta es solo esto). En este caso el exceso de falsos positivos no es grave, pero si queremos aplicar esta técnica en un código con más posibilidades, vamos a necesitar filtrar el resultado correcto. Ahí es donde entra la noción que llamaremos menú, aunque también puede que lo encuentren como wordlist, hotwords, o varias formas más.

> Un **menú** en cracking es una cadena, una palabra, o un conjunto de ellas, que esperamos encontrar en el mensaje original, y que por lo tanto nos pueden servir para identificar el resultado positivo.

Pensar un menú puede ser una tarea difícil si nos basamos únicamente en la programación, ya que en sí no hay razones para que una computadora diferencie entre una cadena sea o no parte del cuerpo de palabras de una lengua (aunque claro que podemos aplicar técnicas para que eso ocurra, pero eso ya es otra historia), sin embargo, pensarlo desde el factor humano, en ocasiones, puede ahorrarnos millones de intentos. En nuestro ejemplo, podríamos pensar como menú la palabra cadena "bhf", o los miembros del grupo, o algunas fechas conocidas. Veamos un ejemplo:

```
message = "cjqtc rqfgoqu wuct gn eqfkiq fg djh rctc ocpfct ogpuclgu ekhtcfqu."

for n in range(26):
    res = Cesar Crypter(message, n)
    if "bhf" in result:
        print(res)

>output:
>ahora podemos usar el código de bhf para mandar mensajes cifrados.
```

>tip: En casos parecidos, en que no sabemos nada respecto al mensaje, podemos usar una lista >de palabras de uso frecuente, por ejemplo ['para', 'ella', 'programa', 'dinero','etc',]

Hasta aquí hemos repasado conceptos como básicos como lo son criptografía, cracking, código, encriptación y menú o wordlist. Por curioso que resulte, estas técnicas básicas las aplicaremos con frecuencia. Muchos de los programas conocidos como hashcat, John The Ripper, aircrack, aplican esta misma metodología, claro que en escenarios mas complejos.

Espero que les haya gustado, y que les ha parecido sencillo, o al menos no tan complejo. Más adelante veremos como estas técnicas pueden combinarse con muchas otras, y también algunas formas para defendernos de ellas.

Happy cracking!