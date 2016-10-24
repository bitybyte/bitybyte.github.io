---
layout: post
title: 4 Redes Complejas Increibles - De Facebook al FC Barcelona
author: Pablo Estrada
author_site: http://iampablo.me
tags:
  - español
  - cs
  - machinelearning
  - data

---
Uno de mis temas favoritos en análisis de datos es el análisis de Redes
Complejas. El estudio de relaciones entre múltiples agentes nos permite
entender características interesantísimas de los sistemas en que se
desenvuelven. Vamos a estudiar algunas visualizaciones y ejemplos reales de
redes sociales, y de información que nos han ayudado a descubrir y entender el
mundo que nos rodea.

## Visualización - El mapa de conexiones
En Diciembre de 2010, Paul Butler, en ese entonces un intern de visualización
de datos en Facebook, estaba estudiando el grafo de amistades en Facebook.
En ese entonces, Facebook tenía 500 millones de usuarios, así que tuvo que
trabajar con [Apache Hive](http://es.wikipedia.org/wiki/Apache_Hive), que
permite correr consultas tipo SQL en datos complejos en Hadoop.

Paul tomó una muestra de 10 millones de **pares de amigos**, y después contó
cuántos pares de amigos había entre cada par de ciudades. Es decir, considerando
unas 500 ciudades, redujo una base de datos de 10 millones de elementos, a una
base de datos de unos 125,000 elementos que representaban el número de amigos
entre dos ciudades. Ese resultado lo tomó en R, y lo graficó. El resultado fue
el siguiente:

![Friendship map]( {{ site.url }}/images/friendship_borders.png )

**In-cre-i-ble**. Las conexiones entre ciudades de todo el mundo terminaron
dibujando el mapa completo del mundo! Nota que la gráfica no tiene fronteras
dibujadas en ningún lado: Puras amistades.

Algunas cosas interesantes: Los países están muy conectados entre si. Ejemplos
interesantes son Indonesia, las Filipinas y hasta Hawaii, que a pesar de ser
islas separadas, muestran conexiones muy fuertes ya que son el mismo país. La
ausencia de China.

Me encanta este ejemplo, porque aunque no es un artículo científico publicado,
sí es una visualización padrísima y que nos puede enseñar mucho. Puedes checar
[el post completo](https://www.facebook.com/notes/facebook-engineering/visualizing-friendships/469716398919/).

## Búsqueda local - Seis grados de separación
Stanley Milgram, profesor de psicología en la Universidad de Yale en Estados
Unidos condujo un experimento donde eligió personas aleatoriamente en la zona
media de los Estados Unidos (Nebraska, Kansas), y les entregó una carta dirigida
a una persona en Boston, en la costa Este (a unos 2300 kilómetros de distancia).
Sin embargo, les pidió que le entregaran la carta uno por uno a gente que
__conociera personalmente__, tratando de *acercarla* a su destino en Boston.

Los resultados fueron súper interesantes. Los investigadores enviaron un total
de casi 300 cartas, de las cuales:

1. Un total de 64 cartas llegaron a su destino. Esto no es muy sorprendente.

2. En promedio, una carta pasó por 5 o 6 personas para llegar a su destino.

3. Hubo algunas cartas que dieron hasta 9 o 10 saltos

4. También hubo algunas cartas que dieron muy pocos saltos: Uno o dos.

![6 degrees of separation]( {{ site.url }}/images/6_degrees_map.gif )

El experimento fue muy famoso, y cambió la manera en que se estudian y se
entienden las redes sociales. Aún en redes sociales con grandes poblaciones
(en ese entonces, Estados Unidos tenía 200 millones de habitantes), una persona
nunca está demasiado lejos de otra - y el mundo de verdad es pequeño!

[El artículo de Stanley Milgram](http://measure.igpp.ucla.edu/GK12-SEE-LA/Lesson_Files_09/Tina_Wey/TW_social_networks_Milgram_1967_small_world_problem.pdf)
apareció en una revista de divulgación. Chécalo!

Hace unos meses, investigadores en Facebook calcularon la distribución de los
grados de separación entre cada par de personas en la red social de Facebook.
Resulta que el número promedio es mucho más pequeño: 3.57 ; ). [Checa también el
post de Facebook](https://research.facebook.com/blog/three-and-a-half-degrees-of-separation/).

## Estructuras - Qué pequeño es el mundo!
El fenómeno encontrado por Stanley Milgram en su artículo resultó ser uno muy
común en todo tipo de redes sociales. Una de las características que explican
redes de este estilo es la existencia de algunos elementos de la red que están
extremadamente conectados: En redes sociales (por ejemplo Facebook), el promedio
de amigos por persona es relativamente bajo: Unos 100. Sin embargo, existen
algunas **pocas** personas que tienen **miles** de amigos. Interesantemente,
estas personas permiten crear una red mucho más conectada.

Casos como éstos existen en redes que no sólo son sociales. Por ejemplo:

* [La red de sitios web](http://internet-map.net/), donde cada nodo es un sitio
web, y un enlace es un link a otro sitio web. En esta red, el promedio de links
que apuntan a un sitio web es muy, muy pequeño, pero existen *algunos* sitios
web que tienen miles o incluso millones de links que les apuntan.

* [La red de colaboración de actores de Hollywood](https://en.wikipedia.org/wiki/Six_Degrees_of_Kevin_Bacon),
y la [red de colaboraciones académicas en matemáticas](https://en.wikipedia.org/wiki/Erd%C5%91s_number)
son otras dos redes donde la distancia mínima entre elementos es
sorprendentemente baja.

Este fenómeno fue explicado por dos profesores de origen Húngaro: Laszlo
Barabasi, y Reka Albert. Ellos también acuñaron el término de **acoplamiento,
o adhesión preferencial**. Es decir que cuando una red está creciendo,
existen nodos que tienen una mayor probabilidad de atraer a los nodos nuevos.
Por ejemplo, los actores más activos colaboran con actores más jóvenes;
los sitios web más populares atraen más y más links, etc.

Los investigadores propusieron un modelo matemático de una red en crecimiento,
donde nodos nuevos se enlazan a nodos existentes con una **probabilidad
proporcional al número de enlaces del nodo existente**. Es decir que la
popularidad de un nodo, depende de **su popularidad**. ¿Confuso? Un poco -
pero muy interesante.

Albert y Barabasi [publicaron un artículo científico](https://arxiv.org/pdf/cond-mat/0106096.pdf)
donde proponen su teoría y la defienden, en el año de 1999. Y hablando del rey
de roma, el artículo es uno de los artículos más populares de la historia, con
más de 26,000 citas. Resulta que la red de artículos científicos **también es
una red con adhesión preferencial.**

![Network science papers]( {{ site.url }}/images/milgram_citations.png )
Figura. Red de citas de artículos científicos. Podemos ver el artículo de Stanley
Milgram en 1967, el de Barabasi-Albert de 1999, y otros importantes artículos
que estudian redes complejas! SO META!

## Buscando patrones - El Barça de verdad es único
En el año de 2014, un grupo de investigadores en Telefónica España, y en el
Instituto de Investigación de Cómputo de Katar obtuvieron datos de pases de
equipos de la liga de fútbol Española, y construyeron pequeños grafos sobre el
tipo de pases - y secuencias de pases que cada equipo efectuaba.

Los investigadores estudiaron los patrones en secuencias de tres pases. En
particular, tipificaron las secuencias según el número de jugadores que
participan en ellas, y el orden en que participan. Es decir:

* Pase ABAB - Es una secuencia de pase donde dos jugadores intercambian el
balón dos veces: Jugador A - Jugador B - Jugador A - Jugador B.

* Pase ABCD - Es una secuencia con cuatro jugadores: Jugador A - Jugador B -
Jugador C - Jugador D.

* Pase ABCA - Secuencia con tres jugadores donde Jugador A recibe el balón de
nuevo al final: Jugador A - Jugador B - Jugador C - Jugador A (de nuevo).

Vamos a hacer una prueba rápida: Dadas estas consideraciones, considerando que
el primer jugador en la jugada es A, el segundo jugador en la jugada es B, y el
tercer y cuarto jugadores en **aparecer** son C y D - si es que aparecen;
entonces el total de combinaciones distintas es de 5: ABAB, ABAC, ABCA, ABCB y
ABCD.

Al hacer este análisis y medir la frecuencia con que cada equipo utiliza cada
tipo de secuencia, descubrieron algo muy interesante: Casi todos los equipos de
la liga española tienen patrones similares, pero el **FC Barcelona** parece
tener un estilo muy muy único.

El Barcelona utiliza secuencias tipo ABAB y ABCB **mucho más** que los otros
equipos, y utiliza secuencias tipo ABCD y ABCA **bastante menos** que el resto
de equipos de la liga. ¿Qué significa esto?

Si lo pensamos un poco, esto puede ser una manifestación del estilo Tiki-taka
del Barça, donde utilizan muchos pases cortos entre grupos pequeños de jugadores.
Por eso es que los pases ABAB y ABCB, que presentan intercambios de balón entre
dos jugadores (A-B-A-B, y B-C-B) son mucho más prevalentes, mientras que las
secuencias más *estándar* como ABCD son menos comunes.

De hecho, los investigadores hicieron análisis de componentes principales con
los datos, y encontraron lo siguiente:

![Barca clustering]( {{ site.url }}/images/barca_clustering.png)

Según los patrones de nuestros datos, el estilo de juego del Barça es
**verdaderamente** único. Y esto lo sabemos gracias a que podemos estudiar sus
redes de pases! Si quieres saber más, puedes revisar [el artículo científico en
ArXiV](https://arxiv.org/pdf/1409.0308v1.pdf).

## Concluyendo
Como vemos, muchos resultados interesantes pueden ser obtenidos con el estudio
de redes complejas. En temas desde la manera en que nos relacionamos con otros,
qué tan interconectado está el mundo, y hasta los estilos secretos de fútbol.

Las redes complejas son un campo nuevo. Si te interesa saber más, puedes checar
[este curso en Coursera](https://www.coursera.org/learn/social-economic-networks#syllabus).

Si quieres saber más, comentarios y preguntas son bienvenidos!
