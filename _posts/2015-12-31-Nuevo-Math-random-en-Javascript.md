---
layout: post
title: Hay de Math.random() a Math.random()
author: Pablo Estrada
author_site: http://iampablo.me
language: spanish
tags:
  - español
  - cs
  - javascript
  - randomness
 
---
JavaScript es uno de los lenguajes de programación más utilizados en el mundo; y la función `Math.random()` es la función estándar de JavaScript para obtener números pseudoaleatorios. ¿Me creerían si les dijera que hasta hace unos días, la función `Math.random()` en uno de los motores de JavaScript más usados en el mundo era medio mala?

Pues sí: hace unos días, el algoritmo para generar números pseudoaleatorios que se utiliza en el motor de JavaScript V8 fue modificado. Esto después de que usuarios encontraran problemas en sus servidores que dependían de un generador de números aleatorios confiable.

En este post vamos a estudiar los Generadores de Números Pseudoaleatorios (también conocidos como PRNGs por sus [siglas en inglés](https://en.wikipedia.org/wiki/Pseudorandom_number_generator)), de cómo los desarrolladores de [V8](https://developers.google.com/v8/intro) utilizaron uno bastante malo - y de cómo los cacharon con las manos en la masa.

Pero entonces, ¿porqué era tan malo el generador de Javascript? y en general, ¿qué determina que un Generador de Números Pseudoaleatorios sea bueno?

## ¿Qué es aleatorio y cómo lo generamos?
*<small>Cualquiera que piense en métodos aritméticos para generar números aleatorios está, por supuesto, en pecado.
— JOHN VON NEUMANN (1951)</small>*
{: align="right"}

En la ciencia de la computación y las matemáticas es común que se necesite una 'fuente de aleatoriedad'. Por ejemplo, para criptografía, juegos y apuestas, [simulaciones de Montecarlo](https://es.wikipedia.org/wiki/M%C3%A9todo_de_Montecarlo), o incluso para cosas sencillas como el [algoritmo QuickSort](https://es.wikipedia.org/wiki/Quicksort) se necesita generar múchos números que sean aleatorios, y que sean generables rápidamente. Desafortunadamente, generar números veraderamente aleatorios no es sencillo. Existen aplicaciones donde es muy importante que los números sean verdaderamente aleatorios, como la criptografía o las apuestas. Para estas aplicaciones existen generadores basados en eventos cuánticos o en sistemas caóticos (e.g. ruido atmosférico, dispositivos mecánicos, etc.). Estos generadores generan números *verdaderamente* aleatorios. Posteriormente escribiré un post sobre ellos.

Para otras aplicaciones, no es tan importante que los números generados sean totalmente aleatorios. Basta con que sean *bastante* aleatorios. Para eso fueron crados los Generadores de Números Pseudoaleatorios, que utilizan un 'estado interno' al que se le aplica una operación determinística para generar una secuencia de números. Es decir que estos generadores funcionan algo así:

{%highlight javascript %}
var estado = VALOR_INICIAL;
function random(){
    estado = operacion_deterministica(estado);
    return estado; // estado es el número 'aleatorio' producido
}
{% endhighlight %}

¿Qué? ¿Operación determinística? Entonces no son aleatorios! (véase la cita de John Von Neumann al principio de esta sección)- Así es. Los generadores de números pseudoaleatorios generan una secuencia de números que *no es* aleatoria, pero *parece ser* aleatoria. Observen que dado el mismo **valor inicial**, la secuencia de números producida sería la misma.

Resulta que existen varias funciones aritméticas que pueden utilizarse para generar secuencias que *parecen ser* bastante aleatorias. Estas funciones han sido estudiadas extensivamente, y se ha escrito bastante sobre el tema. Por ejemplo, el renombrado profesor [Donald Knuth](https://es.wikipedia.org/wiki/Donald_Knuth) de la Universidad de Stanford le dedica [un capítulo](http://www.informit.com/articles/article.aspx?p=2221790) de su serie de libros *The Art of Computer Programming* a la generación de números aleatorios y pseudoaleatorios. Entre otras cosas, Knuth concluye que un generador de números aleatorio no puede ser *una funcion cualquiera*, sino que tiene que ser una función diseñada y seleccionada *muy* cuidadosamente.

## ¿Qué es un PRNG?
Con lo que hemos hablado, vamos a observar un ejemplo de PRNG que funciona con un método conocido como congruencial lineal. Se llama de esta manera porque es una función 'lineal por partes'. Esta función se construye con dos números primos 'grandes'. En este caso, vamos a restringir nuestro número a generar a que esté entre 0 y 15, así que nuestros primos pueden ser 5 y 3. Bastante grandes para nuestro rango. Nuestra función congruencial lineal para generar números pseudoaleatorios es la siguiente:

{%highlight javascript %}
var estado = VALOR_INICIAL % 16;
function random(){
    estado = (estado * 5 + 3) % 16
    return estado;
}
{% endhighlight %}

La secuencia de números que puede ser generada con este PRNG es la siguiente:

<center> <img src="http://pabloem.github.io/images/prng_1.png"></img><br>
Figura 1. Números generados por un PRNG congruencial lineal. </center>

Como pueden observar, la secuencia se repite cada 16 números generados. Tómense un minuto para ver cómo se genera cada uno de los números. También, como pueden ver, la secuencia parece ser bastante aleatoria (aunque en realidad no lo es).

Vamos a ver entonces, qué características son importantes al diseñar un PRNG.

## Diseñando PRNGs
Una de las características más importantes de un PRNG es su periodicidad. Ya hemos mencionado que un PRNG tiene un estado interno, con base en el cual genera una secuencia de números. Dado un estado inicial en particular, un mismo algoritmo generará la misma secuencia. Y como estamos trabajando con *una computadora*, todas éstas son variables limitadas: Es decir que hay un número *finito* de estados internos que puede generarse.

Entonces, sabemos lo siguiente:

* Dado el mismo valor inicial, la secuencia generada por un PRNG es siempre *la misma*
* La cantidad de números distintos que pueden ser generados es *finita*

Esto significa que la **secuencia generada por un PRNG es de una longitud finita**, y **eventualmente empieza a repetirse a sí misma**. Pueden observar esto en la secuencia de números en la Figura 1.

La longitud de la máxima secuencia de números distintos que puede ser generada por un PRNG es conocida como el *periodo* de este PRNG. Como es deseable generar secuencias que sean distintas, las funciones utilizadas en el PRNG deben tener periodos tan largos como sea posible.

Si el estado interno de un PRNG tiene una representación de **k bits**, entonces el periodo del PRNG es *como máximo* 2ᵏ. Los buenos PRNGs deben tratar de alcanzar esta periodicidad. Si no, solamente están desperdiciando memoria.

El otro criterio importante para un PRNG es que el algoritmo produzca secuencias que tenga una distribución uniforme, y que muestren poca correlación entre valores sucesivos.

## ¿Cómo fue encontrado el problema?
El problema fue descubierto por [Betable](https://en.wikipedia.org/wiki/Betable), una compañía que ofrece una plataforma de apuestas en línea. El director general de Betable, Mike Malone detalló lo ocurrido [en un post en el blog de ingeniería de Betable](https://medium.com/@betable/tifu-by-using-math-random-f1c308c4fd9d#.y78a8vmrd). Básicamente, en uno de sus servicios, que estaba programado en Javascript, utilizaron una función para generar identificadores aleatorios para sus transacciones. Su función para generar estos identificadores es simple:

{% highlight javascript %}
var ALPHABET = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789-_';

function random_base64(length) {
    var str = "";
    for (var i=0; i < length; ++i) {
        var rand = Math.floor(Math.random() * ALPHABET.length);
        str += ALPHABET.substring(rand, rand+1);
    }
    return str;
}
{% endhighlight %}

En Betable utilizaron identificadores de 22 caracteres de longitud, que vienen de un alfabeto con 64 caracteres. Esto significa que el *espacio* de identificadores es de tamaño 64²², es decir, de más o menos 2¹³². Bastante grandecito!

En Betable hicieron cuentas. Según sus números, si seleccionaramos uniformemente de este espacio a una taza de 1 millón de elementos por segundo por 300 años, la probabilidad de una colisión sería de 1 en 6 mil millones. ¿Cómo lo calcularon? Modelándolo como una [Paradoja del Cumpleaños](https://es.wikipedia.org/wiki/Paradoja_del_cumplea%C3%B1os) (un tema muy interesante, denle un vistazo!).

Sin embargo, tras **un mes** empezaron a observar problemas donde varias transacciones recibían **los mismos identificadores**. Dado que el modelado del expacio de identificadores es correcto, no les quedó más que verificar cómo funciona el PRNG de Javascript.

## El `Math.random` de antes y de ahora
El tipo numérico en Javascript es [IEEE 754 binary64](https://en.wikipedia.org/wiki/Double-precision_floating-point_format), así que cuenta con 53 bits para generar números entre 0 y 1. Esto significa que en vez de poder generar 2¹³² diferentes identificadores, serían cuando mucho 2⁵² identificadores *por cada variable que se utilice para guardar estado*. Vale, no es tan bueno - pero funciona... sin embargo, al investigar más, en Betable descubrieron más problemas en el generador de V8.

Por cierto, `Math.random` es parte de [la especificación oficial de Javascript](http://tc39.github.io/ecma262/#sec-math.random); donde se indica que devuelve un número mayor o igual a 0 y menor a 1, con una distribución más o menos uniforme. La especificación no dice nada sobre el algoritmo, la periodicidad y la calidad de la distribución.

El algoritmo que utilizaban en V8 para `Math.random` es el siguiente:

{% highlight javascript %}
var MAX_RAND = Math.pow(2, 32);
var state = [seed(), seed()];

var mwc1616 = function mwc1616() {
    var r0 = (18030 * (state[0] & 0xFFFF)) + (state[0] >>> 16) | 0;
    var r1 = (36969 * (state[1] & 0xFFFF)) + (state[1] >>> 16) | 0;
    state = [r0, r1];

    var x = ((r0 << 16) + (r1 & 0xFFFF)) | 0;
    if (x < 0) {
        x = x + MAX_RAND;
    }
    return x / MAX_RAND;
}
{% endhighlight %}

Este algoritmo, según los comentarios en el código de V8 es el "[Multiply-with-carry de George Marsaglia](https://en.wikipedia.org/wiki/Multiply-with-carry)". Por cierto, [George Marsaglia](https://en.wikipedia.org/wiki/George_Marsaglia) era un matemático Estadounidense que pasó su carrera estudiando PRNGs - osea que no parece mala idea utilizar esta función como PRNG... pero entonces?

Pues si se fijan en este generador, genera 32 bits con base en dos variables. Lo hace con xors tras realizar corrimientos de 16 bits en cada dirección. Si se fijan, los 16 bits 'altos' del número generado vienen de `r0`, mientras que los 16 bits 'bajos' vienen de `r1`. Esto causa que la periodicidad de un generador como el de Betable donde se utiliza un redondeo de tipo `Math.floor(Math.random() * arr.length);` (donde sólo se utilizan los bits más altos del número generado) dependan de una sola semilla de 16 bits. Es decir que su periodo es *muy* corto.

Pero ¿Porqué sólo se utilizan los bits altos del número? Imaginen que `Math.random` genera el número binario `0.1010001100101`. Ahora, en el código de Betable, nuestra variable `ALPHABET` tiene una longitud de 64 caracteres. 64 es 2⁵. Es decir que nuestro número generado, lo multiplicamos por 2⁵, que en binario es `100000`. Es decir `result = 100000*0.1010001100101 = 10100.01100101`. Luego, hacemos `Math.floor` a ese resultado, que no es más que un truncamiento. El número que devolvemos es `10100`, que no es más que los 5 bits más altos del número generado por `Math.random`.

Dado que esos números dependen de una sola semilla de 16 bits, como ya dije, el periodo es *mucho* más corto.

Y por eso es que en Betable empezaron a encontrar problemas a tan solo un mes.

Para darle una forma visual a la calidad de los PRNGs de en Javascript, observen cómo en la figura 2 se pueden ver 'imágenes de ruido' generadas con los PRNGs de (1) Safari y de (2) V8. Es bastante notorio que el PRNG de Safari genera una imagen que *se ve* más aleatoria.

<center><img src="https://cdn-images-1.medium.com/max/600/1*G2aXFFqqUane0d1Maw-fPw.png"></img><img src="https://cdn-images-1.medium.com/max/600/1*dpaeqFkE-kqW9SR6UOTZBw.png"></img><br>
Figura 2. Ruido generado por (1) Safari y (2) V8 (Chrome) - por M. Malone de Betable.
</center>

## El nuevo `Math.random`
El post de Malone en Betable fue muy popular (¿y cómo no? Es un análisis profundo y muy bueno de V8 y su PRNG); y eventualmente los desarrolladores de V8 se enteraron - y claro, lo tenían que arreglar : )

El algoritmo que seleccionaron fue [xorshift128+](http://v8project.blogspot.kr/2015/12/theres-mathrandom-and-then-theres.html), un algoritmo rápido, con periodicidad de 2¹²⁸-1, y que pasa la mayor parte de los tests básicos de aleatoriedad. El nuevo código se ve así:

{% highlight c %}
uint64_t state0 = 1;
uint64_t state1 = 2;
uint64_t xorshift128plus() {
  uint64_t s1 = state0;
  uint64_t s0 = state1;
  state0 = s0;
  s1 ^= s1 << 23;
  s1 ^= s1 >> 17;
  s1 ^= s0;
  s1 ^= s0 >> 26;
  state1 = s1;
  return state0 + state1;
}
{% endhighlight %}

Específicamente, el código está disponible en [el repositorio de V8 en Github](https://github.com/v8/v8/blob/085fed0fb5c3b0136827b5d7c190b4bd1c23a23e/src/base/utils/random-number-generator.h#L102). (viva el open source!)

Claro, no hay que olvidar que este tipo de generadores no son aptos para aplicaciones criptográficas (de seguridad), o de apuestas oficiales; pero simulación y cómputo distribuído son aplicaciones válidas para un PRNG de este tipo.

Y con esto, el drama del mal PRNG en V8 se termina. Tengan en cuenta que el Chrome que tienen instalado en sus computadoras tardará algunos días, semanas o meses en tener este nuevo algoritmo incorporado, así que no vayan a basar apuestas en él todavía! ; )

Por cierto, para cualquier curioso, chequen [el análisis de Mike Malone](https://medium.com/@betable/tifu-by-using-math-random-f1c308c4fd9d#.2n9d9689l), que es mucho más completo que el mío, y mucho más informado. De nuevo, espero que les haya agradado el post y hayan aprendido algo.

Recuerden la moraleja: Antes de usar un PRNG, asegúrense de que sean aptos para la aplicación que necesitan, y de que pasan suficientes pruebas de aleatoreidad.

Y claro, como la gente en Betable, no tengan miedo de darse un clavado al código de las librerías que utilizan!

Hasta la próxima, y felices fiestas ; ).
