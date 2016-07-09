---
layout: post
title: Un Panorama del fenómeno Big Data
author: Pablo Estrada
author_site: http://iampablo.me
tags:
  - español
  - cs
  - bigdata
  
---
En los últimos años, **Big Data** se ha convertido en una de las expresiones favoritas del mundo de la computación. Empresas grandes y pequeñas han buscado 'adoptar' el Big Data, y muchas otras han decidido 'enseñarlo'. El campo es un poquito confuso y no es fácil encontrar un buen resumen de qué es big data, y cómo funciona. Así que aquí te lo vamos a platicar! Antes de tomar un curso, o rendirte, checa este post para darte una idea de qué onda con el Big Data!

## ¿Qué es Big Data?
Pues para empezar, ¿A qué se refiere uno cuando dice *Big Data*? Se le d el término de Big Data a *datasets* que son demasiado grandes para ser analizados con herramientas tradicionales de análisis de datos.

¿Cómo?

#### Un ejemplo

Imagínate que tienes una lista de 1,000 números en un archivo, y quieres obtener el 5o número más grande que hay en el archivo. ¿Cómo le haces? En pseudocódigo es algo así:

{% highlight python %}
def quinto_mayor(archivo):
    lista = leer_archivo("lista.txt")
    ordenar(lista)
    return lista[5]
{% endhighlight %}

Ahora, imagínate que la lista tiene 20,000,000,000 (veinte mil millones) de números. Es decir que el archivo tendría un tamaño de 20GB (por lo menos). ¿Observas cuál sería el problema con nuestro programa anterior?

Pues el problema es: **No podrías leer el archivo a la memoria**. Tu programa ocuparía toda la memoria disponible, y eventualmente el sistema operativo dejaría de darle más memoria; y el programa fallaría. **En casos como éste es donde necesitamos técnicas nuevas para analizar los datos.**

## ¿A quién se le ocurrió?
Parece obvio: Una de las primeras empresas en contar con cantidades astronómicas de datos fue Google. Eso juntado con la cultura de la compañía permitió que, desde dentro, Google fuera generando herramientas para analizar volúmenes grandes de datos. Muchas de estas herramientas fueron diseñadas para analizar logs y 

En 2003 y 2004, investigadores de Google publicaron dos artículos importantísimos: En el primero definieron [Google File System (GFS)](http://static.googleusercontent.com/media/research.google.com/en//archive/gfs-sosp2003.pdf), un sistema de archivos para clusters, que busca ser **confiable** y **robusto contra errores**. Posteriormente, en el segundo artículo definieron [MapReduce](http://static.googleusercontent.com/media/research.google.com/en//archive/mapreduce-osdi04.pdf), que es un esquema para analizar conjuntos grandes de datos utilizando **clusters**.

Estos dos artículos fueron muy influyentes. Un investigador llamado [Doug Cutting](https://en.wikipedia.org/wiki/Doug_Cutting) que trabajaba en Yahoo leyó esos artículos, y con ellos construyó la base para la primera plataforma de análisis de grandes datos: **Apache Hadoop**.

# Hadoop y MapReduce
Hadoop fue la primera plataforma de **Big Data**, y sigue siendo una de las plataformas más importantes. Fue lanzado en 2006, y desde entonces fue adoptado por [**un sinnúmero de organizaciones**](https://www.cloudera.com/partners/partners-listing.html). Esto permitió que se crearan muchas herramientas y plataformas que funcionan con Hadoop.

La idea básica detrás de Hadoop es **Map-Reduce**. Para realizar análisis de cierto tipo de datos, es necesario dividir el análisis en dos rutinas básicas: La rutina **Map** y la rutina **Reduce**.

La rutina **Map** toma ciertos datos en su forma *cruda*, o no-estructurada, y **"emite"** datos en forma de pares clave-valor (key-value pairs). **Por ejemplo**, la función map puede recibir **texto**, y devolver un conteo de palabras.

Posteriormente, Hadoop obtiene los key-value pairs de Map, los ordena por clave, y los envía a una rutina **Reduce**, que realiza operaciones que 'agregan' esos datos y *emite* resultados finales.

Vamos a ver un ejemplo simplificado en Python que encontré en [este blog](http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/).

### Un ejemplo
Vamos a recibir texto largo, y queremos encontrar las **palabras más comunes** en este texto. Para esto tenemos que programar las rutinas **Map** y **Reduce**, que hagan lo siguiente:

1. Map obtiene líneas de texto, e imprime palabras individuales con su conteo
2. Reduce cuenta cuántas veces ocurre una misma palabra

#### Map
{% highlight python %}
def map(in_text):
    in_text = in_text.strip() # Removemos espacios en extremos
    words = in_text.split() # Separamos por palabra
    for w in words:
        print("{}\t{}".format(w, 1)) # Palabra w aparece una vez
{% endhighlight %}

#### Reduce
**Nota**: Esta rutina funciona porque Hadoop ordena las palabras antes de pasarlas a la rutina redue.
{% highlight python %}
def reduce(in_stream):
    current_word = None
    count = 0
    for w, c in in_stream:
        if w != current_word:
            if current_word is not None:
                print("{}\t{}".format(current_word, count)
            current_word = w
            count = 0
        count += int(c)
{% endhighlight %}

Podemos ver que el modelo de programación de Hadoop es un poco raro. Y hay que ser creativos a la hora de programa *pipelines* en Hadoop. De hecho, [hay](https://jobs.apple.com/search?job=49791779&openJobId=49791779#&openJobId=49791779) [muchos](https://cognizant.taleo.net/careersection/1nacorp/jobdetail.ftl?job=626620&src=JB-11160) [trabajos](https://careers.bloomberg.com/job/detail/50188?utm_source=Indeed&utm_campaign=Bloomberg_Indeed) [que](http://jobs.adeptsolutionsinc.com/candidates/myjobs/openjob_outside.jsp?a=f9jdnw9axoq1t0ibwwckf3f78rs99403cepglquabe29vrufy1mwpeayofgomgdh&SearchString=&StatesString=&source=indeed.com&id=8676921) buscan gente con experiencia en Hadoop. 

# ¿Y luego?
Hadoop se volvió muy, **muy** popular. Fue adoptado y utilizado por muchísimas compañías, y sigue siendo una de las plataformas más populares en la industria. Sin embargo, a muchos les pareció que el modelo de programación de Hadoop (basado en MapReduce) es demasiado estricto, y en ocasiones lento. En respuesta a estas *debilidades*, investigadores en la Universidad de California en Berkeley desarrollaron [Apache Spark](http://spark.apache.org/).

Dado que Spark fue construido basado en Hadoop, puede utilizar muchas de sus ventajas: Spark usa el Hadoop Distributed File System (inspirado en GFS), y puede correr como un cliente *encima de Hadoop*. Pero evita sus debilidades. Debido a esto, en los últimos meses, Spark se ha vuelto extremadamente popular en la comunidad. Las principales ventajas de Spark son:

1. Velocidad. Ejecutar una tarea de análisis en Spark es mucho más rápido que ejecutarla en Hadoop porque Spark trata de mantener los datos en memoria, sin tener que intercambiar con el disco duro tan frecuentemente.
2. Flexibilidad. Mientras el modelo de Hadoop es un tanto rígido, Spark tiene funcionalidad que permite procesar datos en **batch** o en **streams**, puedes correr algoritmos de Machine Learning, y que requieran reiterar sobre los mismos datos, además de queries de SQL - mientras que Hadoop simplemente ofrece procesamiento en Batch.

Spark es construído con una serie de tecnologías nuevas, incluyendo caching y en especial en el lenguaje de programación **Scala**, que es un lenguaje creado para evitar las debilidades del lenguaje Java.

La interfaz de programación de Spark es más simple, y permite desarrollar proyectos más fácilmente, y de manera interactiva. Algo que a muchos les gusta de Spark es que permite realizar tareas en una consola interactiva, más o menos como programar en Python.

En los últimos meses, Spark y Scala se han convertido en los niños más populares de la calle **Big Data**.

### Un ejemplo
En el ejemplo anterior hicimos un contador de palabras en Hadoop. Ahora vamos a ver cómo codificar un contador de palabras en Spark, de nuevo, utilizando el lenguaje Python por simplicidad. Como verán, es muy, muy simple:

{% highlight python %}
text_file = sc.textFile("hdfs://...")
counts = text_file.flatMap(lambda line: line.split(" ")) \
             .map(lambda word: (word, 1)) \
             .reduceByKey(lambda a, b: a + b)
counts.saveAsTextFile("hdfs://...")
{% endhighlight %}

Es tan corto que es difícil seguir el programa. Los pasos son: (1) leer el archivo desde el sistema de archivos, (2) separar las palabras de cada línea, (3) emitir los pares palabra-número, (4) sumar todos los números que corresponden a las mismas palabras.

¿Se dan cuenta de la simpleza? Nada mal eh : )

# El futuro
El fenómeno Big Data es jóven y nuevas innovaciones ocurren todos los días. Hay compañías que apuestan al cómputo en la nube por que administrar clusters no es sencillo. Hay otros que buscan innovar en la manera en que se hace el análisis.

En esta área hay dos iniciativas interesantes: [Apache Flink](https://flink.apache.org/) y [Apache Beam](http://beam.incubator.apache.org/). Ambos proyectos buscan mejorar las herramientas existentes para realizar análisis de datos que son generados continuamente por aplicaciones distribuidas. Particularmente **Apache Beam** fue desarrollado por el mismo Google, y posteriormente su código fue abierto para ser utilizado en la comunidad.

Estas dos plataformas están creciendo rápidamente, y dado que Google ha utilizado su propia experiencia con análisis de datos, probablemente tengan cualidades que las hagan una gran alternativa a Hadoop o Spark. También vienen con oportunidades para desarrollar en sus **comunidades de software libre**, si eso te interesa.

# Concluyendo
Las plataformas para análisis de Big Data han llegado para quedarse. Todas presentan oportunidades interesantes. En este post describimos la idea básica de las plataformas más populares - para que puedas adentrarte a la plataforma que más te interesa y más te atraiga.

Si has decidido aprender alguna de las plataformas personalmente, probablemente lo mejor sea que estudies **Spark**, ya que es popular y útil en la actualidad. De cualquier forma aquí te dejamos algunos tips básicos para que te adentres al mundo de **Big Data** poco a poco:

* Aunque todas las plataformas tienen APIs en varios lenguajes, **todas** ellas empezaron con base en **Java**. Es decir que Java sigue siendo un lenguaje excelente para aprender como desarrollador.
* Antes de jugar con *Big Data* vale la pena trabajar con *Small* Data. Y los lenguajes de scripting son ideales para este tipo de tareas. Trata de aprender **Python**, **Perl** o alguno de sus primos.
* La **programación funcional** está de vuelta. Python, Scala, Java 8 y otros lenguajes le han apostado - y tú deberías aprender también.

Eso es todo! Mucha suerte y bienvenidos son los comentatios ; ).
