---
layout: post
title: Estabilidad numérica y números de punto flotante
author: Pablo Estrada
author_site: http://iampablo.me
tags:
  - español
  - cs
  - math
  - programming

---

Como usuarios, e incluso como programadores, estamos acostumbrados a utilizar
nuestras computadoras y que funcionen. Dependemos de capas y capas concéntricas
de abstracción que permiten ocultar la complejidad de las capas anteriores. Sin
embargo, para un buen programador es importante ser consciente de estas capas,
de las implicaciones y de las mejores maneras de utilizarlas.

Hoy vamos a hablar de los números decimales y su representación interna en la
computadora. Todos los usamos, y dependemos de ellos para muchos de nuestros
programas - sin embargo, resulta que la funcionalidad de los números
decimales es **bastante frágil**, y aunque funcionan muy bien en muchos casos,
hay **muchos otros casos** donde podemos encontrar las debilidades de utilizar
números decimales en una computadora moderna.

## Bits y números decimales
Como ya sabrás, las computadoras digitales representan información en forma de
señales eléctricas. Con base en esto, se llegó a la convención de que la
ausencia o existencia de una señal eléctrica representa un **cero** o un
**uno** en un entero de base dos. Esto funciona muy bien para números enteros,
pero se vuelve más complicado para otros tipos de información, como texto o números decimales.

Existen varias maneras de representar números decimales en una computadora.
Todas dependen de dividir un conjunto de bits en dos o tres partes. La
manera más popular es la representación de punto flotante, que es ejemplificada
por el estándar IEEE 754. Consiste en dividir un conjunto de bits en las
siguientes partes:

* El bit de signo es un bit que expresa el signo del número. Llamémosle $s$.
* El *exponente*, también se le llama *mantisa*, es un conjunto de bits que
representan un *exponente* para el número. Llamémosle $m$.
* La *fracción* es el conjunto de bits que representa el **valor real** del
número. Llamémosle $f$.

En el estándar IEEE 754, la división de un conjunto de 64 bits es la siguiente:

![Numero de punto flotante de 64 bits](https://upload.wikimedia.org/wikipedia/commons/thumb/a/a9/IEEE_754_Double_Floating_Point_Format.svg/1000px-IEEE_754_Double_Floating_Point_Format.svg.png)

Dado un signo, un exponente y una fracción, el número representado
por ellos tres es:

\[signo \times fraccion \times base^{exponente}\]

Entonces, por ejemplo, el número 1.2345 tendría signo $s=0$, fracción $f=12345$
y exponente $m=10^-4$. De tal manera que:

 \[1.2345 = 1 \times 12345 \times 10^{-4}\]

**Nota:** Existen otras maneras de representar números decimales en una
computadora, por ejemplo los [números de punto fijo](https://en.wikipedia.org/wiki/Fixed-point_arithmetic),
pero no vamos a considerar ese tema en este post.

## Entonces ¿Cuál inestabilidad?

Las matemáticas son *perfectas*. El resultado de una operación no depende del
camino que se siga para llegar a ese resultado. Desafortunadamente, los números
de punto flotante no son *representaciones exactas*, sino que son
aproximaciones. Esto significa que en ocasiones, tenemos que ser muy cuidadosos
a la hora de utilizarlos. El ejemplo más sencillo es el siguiente:

{% highlight python %}
BIG = 1000000000000000
0.9 + BIG - BIG == 0.9 + (BIG - BIG)
#False
{% endhighlight %}

¡¿Qué?! ¿Cómo estuvo eso? Normalmente la adición es una operación asociativa y
conmutativa: No debería importar en qué orden la hacemos. ¿Cómo es que ocurrió
esto?

Pues esto ocurre porque los números de punto flotante no tienen *precisión
infinita*. Es decir que hay un límite en qué tan precisos pueden llegar a ser.
Cuando hacemos `0.9 + 1000000000000000`, el resultado debería ser `1000000000000000.9`,
pero Python sólo alcanza a representar `1000000000000000.875`. Para una
explicación detallada, checa la última sección del post.

## ¿Y eso qué?

Ahora dirás *¿Yo para qué quiero sumar números de órdenes tan distintos?* o
*Psss 0.875 es casi 0.9. Por mí está bien*. Pues sí, en eso tienes razón. El
principal problema de los números de punto flotante *no está en que no
sirven con sumas raras*. El problema principal es que **existen algunos
algoritmos que pueden fallar debido a los números de punto flotante**.

Un ejemplo donde la intestabilidad es muy clara es el cálculo de la varianza
de una muestra de números. Matemáticamente, dada una lista $X$ de longitud
$N$, su varianza es:

\[ \frac{\sum X^2 - (\sum X)^2}{N} \]

Simple: El cuadrado de la suma menos la suma de los cuadrados, todo dividido
entre $N$. Matemáticamente, esto es correcto; sin embargo, el utilizar esta
ecuación para calcular la varianza de una distribución en práctica es un **
algoritmo inestable**.

Vamos a ver el siguiente código donde calculamos la varianza de la misma
distribución, pero **variando la media**. Nota que la varianza **no debería
cambiar**:

{% highlight python %}
import numpy as np
N = int(1e8)
data = np.random.uniform(size=N)

np.std(data)**2  # La verdadera varianza. Aproximadamente 1/12.
# 0.08332694403630711

1/(N-1.0) * (np.sum(data**2) - np.sum(data)**2 / N)
# 0.083326944869713787 # 8 dígitos de precisión. Nada mal.

data += 1e4
1/(N-1.0) * (np.sum(data**2) - np.sum(data)**2 / N)
# 0.083349540833495397 # Sólo 4 dígitos de precisión. No muy bien.

data += 1e5
1/(N-1.0) * (np.sum(data**2) - np.sum(data)**2 / N)
# 0.077086720770867201 # Está mal - pero igual no tan lejos.

data += 1e6
1/(N-1.0) * (np.sum(data**2) - np.sum(data)**2 / N)
# -2.6093158660931586 # ¿Qué? Varianza negativa? Eso es imposible!
# A partir de aquí ya no tiene sentido.
{% endhighlight %}

Como ves, si utilizamos este algoritmo **inestable** para calcular la varianza
de nuestros datos, podemos **meternos en problemas**.

## Conclusión
La mayor parte del tiempo, no tienes que preocuparte de cómo funcionan las
distintas capas de tu máquina para ser un desarrollador productivo y hábil; pero
**siempre** es muy importante que entiendas cómo funcionan los sistemas de los
que dependes.

Hay mucho más que platicar sobre los números de punto flotante, puedes continuar
con la sección siguiente, que observa un ejemplo concreto - y después checa
[este post](http://steve.hollasch.net/cgindex/coding/ieeefloat.html) en inglés.

Hasta la próxima! : )

## Representación interna de números decimales
Para concluir el post, vamos a estudiar la representación binaria más de cerca.
Vamos a ver exactamente cómo es que se representan los números de punto flotante
en términos de bits. Para esto vamos a usar las siguientes funciones de Python:

{% highlight python %}
import struct
def binary(num):
    return ''.join(bin(ord(c)).replace('0b', '').rjust(8, '0') for c in struct.pack('!f', num))
def parts(number):
  binary_representation = binary(number)
  return {'sign': binary_representation[0],
          'exponent': binary_representation[1:9],
          'fraction': binary_representation[9:]}
{% endhighlight %}

En el estándar IEEE 754, hay **un bit** de signo, **8 bits** de exponente, y
**23 bits** de fracción. Recuerdas la fórmula de $signo \times fraccion \times base^{exponente}$?
En IEEE 754 es la misma, con las siguientes consideraciones:

* Para el signo, `0` es positivo y `1` es negativo.
* El exponente tiene que representar números positivos o negativos, así que se le
agrega un *sesgo*, que en este caso es de 127. Así que para calcular el exponente
real, **se le resta** 127 a los bits del exponente (por ejemplo, si los bits son
10000000b(128 base 10), el exponente del número es 1).
* La fracción representa un número *normalizado*, así que se asume que casi siempre
tiene un **1 implicito a la izquierda**. Esto quiere decir que si los bits de la
fracción son 100b, en realidad representan 1100b (por el uno implícito).


Con todo esto en cuenta, vamos a ver la representación interna de algunos números
de punto flotante. Empecemos con el número 2:

{% highlight python %}
parts(2.0)
# {'exponent': '10000000',
#  'fraction': '00000000000000000000000',
#  'sign': '0'}
{% endhighlight %}

* El signo es `0`, así que el número es positivo.
* La fracción es `0`, y con el `1` implícito, significa que el número de la fracción es `1b`.
* El exponente es `10000000b` (128 en base 10), y si le restamos el sesgo, el exponente es `1`.

Con base en la fórmula, tenemos que: $+_{(signo)} 1 \times 2^{1} = 2$.

Ahora, vamos a ver el ejemplo anterior, con la variable `BIG` un poco más pequeña
para que nuestro ejemplo funcione con 32 bits (en vez de 64, que es lo que
  Python usa por default).

{% highlight python %}
BIG = 10000000000
parts(BIG)
# {'exponent': '10010110',
#  'fraction': '00110001001011010000000',
#  'sign': '0'}

parts(0.9)
# {'exponent': '01111110',
#  'fraction': '11001100110011001100110',
#  'sign': '0'}
0.9 + BIG - BIG == 0.9 + (BIG - BIG)

parts(BIG+0.9)
# {'exponent': '10010110',
#  'fraction': '00110001001011010000001',
#  'sign': '0'}

{'exponent': '01111110', 'fraction': '11001100110011001100110', 'sign': '0'}
{% endhighlight %}

Alcanzas a ver el problema? Es muy simple: Al sumar `BIG` con `0.9`, se pierde
la mayor parte de los bits de `0.9`. Ves cómo `BIG` y `BIG + 0.9` tienen sólo
**un bit** de diferencia, pero `0.9` tiene una fracción mucho más compleja.

Ahí está todo el chiste.
