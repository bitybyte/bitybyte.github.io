--- 
layout: post 
title: Analizando 4 redes complejas
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
