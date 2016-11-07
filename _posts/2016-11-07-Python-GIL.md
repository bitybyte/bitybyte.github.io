---
layout: post
title: ¿Porqué no puedo usar paralelismo en Python?
author: Pablo Estrada
author_site: http://iampablo.me
tags:
  - español
  - python
  - programming
  
---
Ya tiene muchos años que nuestras computadoras personales incluyen 
microprocesadores con más de un núcleo. Esto le permite a nuestros programas
ejecutar varias tareas en paralelo. Diversos lenguajes de programación permiten
explotar este modelo a través del uso de multiprocesamiento. Ejemplos muy bien
conocidos son la librería de Threads de Java y los hilos POSIX en C.

Curiosamente, un lenguaje de programación que es muy popular tanto para 
principiantes como para programadores experimentados es Python; y es muy poco
conocido como un lenguaje que permita utilizar paralelismo. Vamos a ver
porqué el paralelismo es difícil en Python, y cuál es la característica de
su arquitectura interna a la que esto se debe.

## El Global Interpreter Lock
Pues resulta que Python sí cuenta con librerías que te permiten ejecutar
varias tareas *concurrentemente*. Por ejemplo la librería `threading`, que
permite defininr *hilos* que se ejecutan con tu programa. Algo interesante
es que estos hilos **no** *avanzan* al mismo tiempo. Observa el siguiente
ejemplo:

{% highlight python %}
from time import time
def fibonacci(n):
    if n < 2: return n
    return fibonacci(n-1) + fibonacci(n-2)

start = time()
for i in range(30, 35):
    fibonacci(i)

print('Tardó {} segundos.'.format(time() - start))
## Tardó 3.23 segundos
{% endhighlight %}

Ahora probemos con la librería de hilos:

{% highlight python %}
from threading import Thread
threads = []
start = time()
for i in range(30, 35):
    t = Thread(target=fibonacci, args=(i,))
    t.start()
    threads.append(t)

for t in threads: t.join() # Esperamos a que terminen
print('Tardó {} segundos.'.format(time() - start))
## Tardó 12.43 segundos -- !!!
{% endhighlight %}
Cómo es que se tarda 4 veces más?! (puede variar según el número de núcleos en tu 
computadora, y la capacidad de los núcleos).

Esto es debido al **GIL** de Python. ¿Qué es el GIL? El *Global Interpreter Lock*.
Un *lock* de exclusión mutua, que permite asegurar que sólo un hilo tenga acceso
a cierto recurso. En el caso del GIL, es un lock que fue creado para asegurarse de
que **sólo un hilo a la vez pueda correr en el intérprete de Python**.

En pocas palabras, si estás utilizando el intérprete tradicional de Python 
(CPython), **es imposible** ejecutar código de Python con varios hilos en 
paralelo. ¿Porqué? Pues fue una decisión de los desarrolladores de Python,
ya que simplifica el manejo de memoria, acceso al sistema operativo, etc.

Esto significa que en Python, el multiprocesamiento es *cooperativo*. Es decir
que los distintos hilos en ejecución intercambian el GIL, y el intérprete
procura mantener una distribución justa en el tiempo de ejecución asignado
a cada hilo.

La siguiente figura lo explica gráficamente. Cada hilo *suelta* el GIL cuando
hacen llamadas de sistema o de entrada-salida, y el intérprete asigna el GIL
a uno de los otros hilos.

![Python execution example]( {{ site.url }}/images/pygil_execution.png)

El GIL se ha convertido en una marca básica de Python, y hay muchas librerías 
que han sido desarrolladas asumiendo la existencia del GIL, por lo que aunque ha
habido varios intentos en la comunidad de Python de remover el GIL en CPython, 
se ha optado por mantenerlo.

Y aunque el GIL siempre está contigo, es importante no olvidar que los hilos
sí corren concurrentemente y **sí puede haber carreras de datos**. Para muestra,
un botón. Corre el siguiente ejemplo unas 5 veces en tu máquina:

{% highlight python %}
class Counter(object):
    def __init__(self):
        self.count = 0
    
    def increment(self, n):
        self.count += n

def count_up(counter, how_many):
    for i in range(how_many):
        counter.increment(1)

threads = []
args = (Counter(), 1000)
for i in range(20):
    t = Thread(target=count_up, args=args)
    t.start()
    threads.append(t)

for t in threads: t.join()
print('Counter: {}.'.format(args[0].count))
# El resultado debe ser 20,000.
## Counter: 17982
## Counter: 18811
{% endhighlight %}

Así es: Si dejas que un montón de hilos corran concurrentemente y modifiquen las
mismas variables, vas a **tener condiciones de carrera (race conditions)**. Una
condición de carrera es cuando dos hilos están accediendo a las mismas 
localidades de memoria sin asegurarse de que sus accesos sean consistentes.

En este caso, el problema es que la línea `self.count += 1` consiste de **tres**
instrucciones separadas:

{% highlight python %}
value = getattr(counter, 'count')
result = value + 1
setattr(counter, 'count', result)
{% endhighlight %}

Si los hilos del programa se llegan a interrumpir entre sí durante alguna de 
estas operaciones, el valor de la variable `count` sería **inconsistente**.

## Entonces, ¿Para qué quiero paralelismo en Python?
Así es: No se acelera la ejecución, y además puede haber condiciones de carrera.
Entonces mejor lo hago todo sin `Threads` no? Pues **no necesariamente**. Existen
escenarios donde vale mucho la pena utilizar el paralelismo de Python. Uno de
ellos es *Blocking I/O*: Procesos de entrada/salida, y otras llamadas al sistema
operativo. ¿Porqué? Porque estos procesos **no ocurren dentro del intérprete de 
Python**. Es decir que llamadas al sistema sí pueden ocurrir en paralelo. Un
ejemplo muy simple es el siguiente:

{% highlight python %}
from time import sleep
start = time()
for _ in range(10):
    sleep(1)
print('Tomó {} segundos.'.format(time() - start))
## Tomó 10.01 segundos.

threads = []
start = time()
for _ in range(10):
    t = Thread(target=sleep, args=(1,))
    t.start()
    threads.append(t)
    
for t in threads: t.join()
print('Tomó {} segundos.'.format(time() - start))
## Tomó 1.01 segundos.
{% endhighlight %}

Nada mal, no? Claro, en este caso simplemente estamos llamando a `sleep`, 
pero podríamos estar leyendo un archivo, una página web, o llamando a un 
proceso externo para ejecutarse!

## Aprendiendo más...
Posteriormente habrá otro post sobre cómo ejecutar código en paralelo en Python.
Si quieres saber más sobre la arquitectura interna del intérprete de Python, 
puedes revisar [esta presentación de David Beazley](http://www.dabeaz.com/python/UnderstandingGIL.pdf),
donde explora el papel y el comportamiento del GIL de cerca.

También puedes revisar el [contenido en general sobre el GIL de David Beazley](http://www.dabeaz.com/GIL/).
Es un excelente recurso para entender el GIL, y convertirte en un desarrollador 
avanzado de Python - o aprender sobre arquitectura de sistemas complejos.
