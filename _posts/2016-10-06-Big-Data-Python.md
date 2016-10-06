--- 
layout: post 
title: Un ejemplo real de Big Data en Python
author: Pablo Estrada
author_site: http://iampablo.me 
tags:
  - español
  - bigdata
  - industry
  
--- 
¿Qué es Big Data? Todo mundo habla de Big Data de una manera difusa y poco
concreta. Todas las empresas quieren 'entrarle al Big Data', pero ¿qué significa eso?
En este post vamos a platicar de manera sencila sobre a qué nos referimos con
el término Big Data, y vamos a hacer un pequeño ejemplo en **Python** que permita 
demostrar cuándo y porqué es necesario utilizar herramientas de Big Data.

**NOTA: Este ejemplo utiliza una base de datos de 2.3 GB. Dependiendo del poder
de tu computadora local, puede tomar desde 15 minutos hasta varias horas para 
ejecutarse completamente**

**NOTA: El ejemplo asume que tienes bastante familiaridad con el lenguaje
Python, y se proveen scripts que funcionarán en sistemas Unix.
Particulamente, de Python se utilizan muchas expresiones lambda, función Map,
y otras características funcionales del lenguaje**.

## ¿Qué es Big Data?
Ya en un [post anterior](http://bitybyte.github.io/Panorama-de-Big-Data/) habíamos 
platicado del panorama del Big Data, y de los grandes jugadores en la industria,
tales como [Hadoop](http://hadoop.apache.org/) , [Spark](http://spark.apache.org/) 
y el nuevo [Apache Beam](http://beam.incubator.apache.org/).

Recapitulando, y de manera sencilla, le llamamos **Big Data** a aplicaciones de 
procesamiento de datos donde los datos que buscamos analizar son *demasiados*
para ser analizados de la manera estándar.

## Nuestro ejemplo
El Instituto Nacional de Estadística y Geografía en México publica ediciones continuas
de una base de datos llamada DENUE. En esta base de datos, ellos buscan manterner
información de identificación y de ubicación de distintos tipos de negocio en México. En
la edición de 2015 tienen casi 5 **millones** de negocios. El conjunto de todos
los archivos de texto de la base de datos ocupa un total de 2.3 GB de espacio.
En el mundo real, bases de datos de Big Data pueden ser de cientos o miles de GB, 
pero para este ejemplo, nos enfocaremos en algo más pequeño.

### El formato de los datos
Si te interesa seguir el ejemplo, puedes [Descargar la base de datos](https://gist.github.com/pabloem/1755254018999c64900d99e29259c8aa), 
que comprimida pesa 360 MB. Descomprimida es una serie de 32 archivos en 
[formato CSV](https://es.wikipedia.org/wiki/CSV), que pesa un par de GB. Así puedes obtener los datos:

{% highlight shell %}
$> mkdir bigdata-inegi ; cd bigdata-inegi
$> mkdir data ; cd data
$> wget --no-check-certificate http://storage.googleapis.com/noogler-projects.appspot.com/denue-2015.tar.gz
$> tar -xzvf denue-2015.tar.gz
$> cd ..
{% endhighlight %}

### Preparando librerías
Para esto necesitas tener Python 2.7 instalado. Vamos a hacer nuestro ejemplo
utilizando el nuevo modelo de [Apache Beam](http://beam.incubator.apache.org/),
que es una colaboración de varias empresas y proyectos. Para esto tenemos que
instalar su versión más nueva - vamos a hacerlo con `virtualenv`, por si las moscas ; )

{% highlight shell %}
$> virtualenv venv 
$> source venv/bin/activate
$> git clone https://github.com/apache/incubator-beam.git
$> cd incubator-beam ; git checkout python-sdk
$> cd sdks/python ; python setup.py install --root /
$> cd ../../../ # De vuelta a bigdata-inegi
{% endhighlight %}

Con esto vamos a instalar el paquete `apache_beam` para Python. Es un paquete
que está en continuo desarrollo, y cambiando mucho, pero nuestro ejemplo debe
funcionar sin problema!

### El código

Primero que nada tenemos que hablar del modelo de progamación de Apache Beam.
Apache Beam funciona con tres conceptos básicos: El de `Pipeline`, `PCollection` 
y el de `PTransform`. Con estos tres elementos básicos se forman las cadenas
de análisis de datos en Beam.

Un `Pipeline` es una serie de transformaciones por las que un conjunto debe
pasar. Cuando uno programa en Beam, uno define `Pipeline`s, y posteriormente
las ejecuta.

Un `PCollection` es la manera en que Beam representa colecciones de datos, que
pueden ser arbitrariamente grandes, o pequeñas (Petaabytes, o algunos Kilobytes).

Un `PTransform` es un tipo de transformación que es aplicada a colecciones de 
datos. Una transformación puede ser de tipo *Parallel Do*, *Group By Key*, 
*Combine*, o *Flatten* - para una aclaración de qué significa esto, puedes
checar el final del post, donde hablo a profundidad del modelo de Beam.

Ahora, es importante considerar que en Apache Beam, la sintaxis con que se opera
es un poco especial. Se utiliza el operador `pipe`, es decir: `|` para indicar
que una transformación es aplicada a un `PCollection`. Es decir: `nuevaColeccion = coleccionDatos | transformacionX`
significa que la `transformacionX` va a ser aplicada a la nuestra `coleccionDatos`,
y el resultado de esa operación será una nueva `PCollection` a la que llamamos 
`nuevaColeccion`.

Con estos tres conceptos en mente, podemos empezar a imaginar/preparar un ejemplo
sencillo. Vamos a tratar de buscar los estados más importantes para cada tipo de
negocio que vemos en el DENUE, va?

Para esto, tenemos que hacer lo siguiente:

1. Leer* los datos de todos los estados a una `PCollection`
2. Filtrar los campos que nos interesan (estado y tipo de negocio)
3. Contar cuántos negocios de un tipo hay en cada estado
4. Ordenar la colección de manera que agrupemos elementos del tipo de negocio
   juntos y podamos comparar los estados más importantes.

{% highlight python %}
from apache_beam.io.fileio import CompressionTypes as ct
from apache_beam.utils.options import PipelineOptions

from beam_utils.sources import CsvFileSource
[...]
p = beam.Pipeline(options=opts)

# Ahora leemos el archivo de entrada y aplicamos varias transformaciones
# a nuestos datos. Todo esto lo guardamos en la PCollection *pairSA*
pairSA = (
    p
    | 'read_files' >> beam.io.Read(
        CsvFileSource(opts.inputFile,
                      compression_type = ct.UNCOMPRESSED))

    # Filtramos filas que no tienen las columnas que nos interesan, aplicando
    # la transformacion beam.Filter a nuestra cadena de analisis
    | 'filter_rows' >> beam.Filter(lambda x: ('Entidad federativa' in x and
                                              'Nombre de clase de la actividad' in x))

    # Ahora filtramos las columnas que nos interesan, aplicando beam.Map
    # a nuestra serie de transformaciones
    | 'filter_columns' >> beam.Map(
        lambda x: tuple((x['Entidad federativa'].strip(),
                         x['Nombre de clase de la actividad'].strip())))

    # Finalmente contamos cuantos negocios hay del mismo tipo en cada estado
    # aplicando la transformacion Count.PerElement()
    | 'count_pairs' >> beam.combiners.Count.PerElement()
)
{% endhighlight %}

No te dejes intimidar por la sintaxis que es un poquito rara, pero intuitiva. Te acostumbrarás.

Con esto podemos obtener el número de negocios de cierto tipo en cada estado. Ahora queremos
agruparlos por tipo de negocio y ordenar los estados segúnnúmero de negocios de cada tipo
para saber cuáles son los estados que más se especializan en cada uno. Para esto tenemos que 
aplicar una función de agrupación.

Pero antes de continuar, vale la pena revisar un concepto importante,
que es el de **pares clave-valor** (key-value pairs). Estos representan
datos que son indizados con base en una columna, y son muy utilizados en 
distintas operaciones. En Beam, un par clave-valor es representado por una
*tupla* de Python del tipo `(clave, valor)`, donde la clave debe ser *hasheable*
y el valor puede ser *cualquier cosa*.

Los vamos a ver aplicados en el siguiente cachito de código:

{% highlight python %}
# Primero tenemos que hacer el negocio la CLAVE en una tupla de CLAVE-VALOR
# Por eso lo organizamos en una estructura de (negocio, (estado, conteo))
# Donde negocio es la CLAVE
sortByBiz = (
    pairSA
    |'business as key' >> beam.Map(lambda x: (x[0][1], (x[0][0], x[1])))

    # Ahora agrupamos por CLAVE, de manera que obtengamos una
    # coleccion de (negocio, [(estado, conteo), (estado, conteo),..])
    | 'group by key' >> beam.GroupByKey()

    # Y dentro de esa coleccion ordenamos por CONTEO
    # y juntamos los resultados - para despues guardarlos
    | 'sort then join' >> beam.FlatMap(
        lambda x: [(x[0], y[0], y[1])
                   for y in sorted(x[1], key=lambda y:y[1])])
)

{% endhighlight %}

Finalmente, podemos formatear el resultado en JSON para poder analizarlo posteriormente
con el programa que nos guste.

{% highlight python %}
# Finalmente escribimos a un archivo de salida
(sortByBiz
 | "jsonize" >> beam.Map(lambda x: json.dumps(x))
 | "write out" >> beam.Write(beam.io.TextFileSink(opts.outputFile))
)
{% endhighlight %}

### Corriendo el ejemplo
El ejemplo está subido a un gist para que puedas descargarlo y probarlo. Para
descargar y correr el ejemplo (en Linux/Unix) puedes hacer lo siguiente en tu
consola:

{% highlight shell %}
$> git clone http://gist.github.com/pabloem/1755254018999c64900d99e29259c8aa beam-ex
$> cd beam-ex
$> source setup.sh
$> python analisis.py
{% endhighlight %}

Y con esto puedes inspeccionar el archivo llamado `outputFile_inegi*`,
para ver los resultamos que obtuvimos.

## Concluyendo
Hemos hecho un ejemplo completo de análisis de una base de datos con herramientas de
Big Data. Como puedes ver, el trabajar con volúmenes tan grandes de datos dificulta
un poco el trabajo, y se vuelve necesario utilizar herramientas menos convencionales
y un **modelo nuevo de programación**.

Preguntas y comentarios bienvenidos ; )

## El modelo Beam a (*un poco de*) profundidad
Ahora, para los que les interesa más, podemos estudiar a profundidad algunos
conceptos del modelo de programación de Apache Beam. 

Como dijimos, `PCollection` es el objeto básico en Beam, que representa una colección
de datos que puede ser muy grande o muy pequeña. Esto significa que Beam 
automáticamente maneja la representación y la ubicación de la colección, ya sea en
disco duro, o memoria. Ésta es una de las características principales de software
para análisis de big data: Te permite dejar de preocuparte del espacio de memoria
o de disco duro que utilizas, y te ayuda a enfocarte en cómo operar con ellos.

Ya habíamos mencionado el concepto de `PTransform`, que es un objeto que representa
una transformación aplicada sobre un `PCollection`. También mencionamos que puede ser
de uno de los siguientes tipos:

* *Parallel Do* - Una transformación de este tipo consiste en aplicar una operación
  a cada uno de los elementos de la colección, e integrar la nueva colección con los
  resultados de esta transformación. Por ejemplo, el paso `filter_columns` de nuestro
  ejercicio. En este caso, estamos tomando los elementos uno por uno, y obteniendo
  únicamente sus columnas de estado y tipo de negocio. Esto se puede aplicar en
  paralelo para cada uno de los datos de la coleción.

* *Flatten* - Una transformación de *flatten*, o de *aplanamiento*, consiste en
  tomar una serie de colecciones y combinarlas en una. Por ejemplo, el paso
  `sort_then_join` realiza una operación llamada `FlatMap` que se encarga
  de *generar* y *combinar* una serie de listas ordenadas por número de 
  negocios de cada tipo por cada estado.
  
* *Combine* - Una transformación de *combinación* es la que se realiza cuando
  se toman varios elementos de una colección, y son combinados dentro de un 
  solo elemento. Un ejemplo puede ser buscar el máximo elemento de una colección,
  el promedio, o como en el paso `count_pairs` de nuestro ejercicio, **contar** 
  el número de elementos de cierto tipo.

* *Group By Key* - Finalmente está la transformación de agrupación. Esta 
  formación consiste en agrupar elementos de una colección según su **clave**,
  o **llave**. Esto lo hacemos en el paso `group_by_type` de nuestro ejemplo,
  donde agrupamos todos los negocios por tipo y así podemos ordenar cada
  lista individualmente.

Con estas dos entidades básicas es posible expresar una variedad de operaciones
sobre conjuntos de datos. En un texto posterior podremos platicar de otras
opciones interesantes del proyecto Beam, y de porqué es una interesante
propuesta para el análisis de Big Data en el mundo.
