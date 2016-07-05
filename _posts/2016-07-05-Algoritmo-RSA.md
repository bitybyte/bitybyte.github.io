---
layout: post
title: El algoritmo RSA y la criptografía de llave pública
author: Pablo Estrada
author_site: http://iampablo.me
tags:
  - español
  - cs
  - cryptography
  - programming
  
---
Algunos algoritmos de la ciencia de la computación que son importantísimos son los algoritmos de encripción. Estos algoritmos permiten esconder información para transmitirla en medios públicos, y son la abse de muchísimos servicios del internet.

Resulta que las bases de la criptografía han existido desde hace siglos en la teoría de números. ¿Cómo es que se pasó de un estudio de los números a la protección de información?

Vamos a dar un paso atrás y averigüar cómo funciona un algoritmo de encripción que es utilizado en la vida real. Y eso es lo que vamos a hacer el día de hoy.

En este post vamos a estudiar el algoritmo RSA, que fue desarrollado por tres investigadores en MIT en la segunda mitad de los 70s.

## Teoría de números y sus algoritmos
La [teoría de números](https://es.wikipedia.org/wiki/Teor%C3%ADa_de_n%C3%BAmeros) es la rama de las matemáticas que estudia las propiedades de los números enteros. Por mucho tiempo no fue considerada más que una rama curiosa de las matemáticas. Números? Qué hay que saber? Está el 0, luego el 1 el 2 y 3, y así. ¿Cuál te cuesta trabajo?

Pues resulta que con la llegada de las máquinas digitales, se volvió una rama importantísima, porque es la fundación de muchísimos algoritmos criptográficos.

Los algoritmos criptográficos son algoritmos que ocultan información apoyándose en un problema algorítmico difícil. El algoritmo RSA se apoya en el hecho que la [factorización de enteros](https://es.wikipedia.org/wiki/Factorizaci%C3%B3n_de_enteros) grandes es un problema complejo y muy difícil de resolver.

Para entender los algoritmos criptográficos, y para entender cómo es que protegen información, es importante conocer algunos conceptos básicos de la teoría de números. Por lo pronto, aquí hay una lista de conceptos que van a aparecer más adelante en este post:

* El [teorema fundamental de la Aritmética](https://es.wikipedia.org/wiki/Teorema_fundamental_de_la_aritm%C3%A9tica) expresa que **cualquier número entero y positivo** puede ser expresado como el producto de un **conjunto único** de números primos.

* **Factorización prima de números** es un problema básico de la teoría de números. Consiste en encontrar los factores primos de un número (e.g. los factores de 3162 son 2, 3, 17 y 31). Resulta que factorizar números es un problema **muy difícil**, **especialmente** cuando el número que buscamos factorizar es producto de **dos números primos grandes**. De hecho, el problema es de **complejidad NP** (para saber un poquito sobre teoría de la complejidad checa [éste post]({{ site.url }}/Isomorfismo-de-grafos/)), y es la base de muchos algoritmos criptográficos.

* El [Algoritmo de Euclides](https://es.wikipedia.org/wiki/Algoritmo_de_Euclides). Es un algoritmo que te permite encontrar el **máximo común divisor de dos números**. Es un algoritmo bastante sencillo y **muy rápido**. Este algoritmo es importante porque **si conocemos dos números con un factor en común, es muy fácil encontrar ese factor**.

* (Sáltatelo por ahora) El [Teorema de Euler (léase Oiler)](https://es.wikipedia.org/wiki/Teorema_de_Euler) es el teorema base para que el algoritmo RSA funcione. Establece una relación interesante de divisibilidad entre números que son primos relativos. Vamos a verlo más adelante.

Antes de ver el algoritmo RSA, vamos a estudiar un algoritmo criptográfico más sencillo, publicado por el mismísimo [Alan Turing](https://es.wikipedia.org/wiki/Alan_Turing), y que también se apoya en el hecho de que la factorización de enteros grandes es un problema difícil de resolver.

## Un algoritmo sencillo
El algoritmo criptográfico sencillo funciona así: Dado un mensaje, vamos a asignar números a las letras, y vamos a completar el número resultante para que sea un número primo (en un momentito les explico porqué). Por ejemplo, para el mensaje "victory", si asignamos a las letras los valores $a=01$, $b=02$,...$z=26$, el resultado sería:

|v|i|c|t|o|r|y|
|22|09|03|20|15|18|25|
{: width="300px" align="center"}

Esto nos da el número 22090320151825. Si le agregamos números a la derecha para convertirlo en número primo, podemos agregar el número 13, y el resultado sería:

|v|i|c|t|o|r|y||
|22|09|03|20|15|18|25|13|
{: width="300px" align="center"}

Y el número $ m = 2209032015182513 $ es un número primo. Una vez que se tiene el mensaje, elegimos una contraseña. La **contraseña es otro número primo grande**, por ejemplo $ p = 22801763489 $, que es un *primo conocido*. Dados el mensaje y la contraseña, entonces el mensaje a transmitir es $ m' = m \cdot p $, o la multiplicación $ 2209032015182513\cdot22801763489 $ es decir que $ m' $ es $ 50369825549820718594667857 $. ¿Todo bien hasta ahora? Aquí están los pasos:

0. Obtener un mensaje de texto
1. Convertir el mensaje a un número largo $ m_n $.
2. A $ m_n $ agregarle números a la derecha para convertirlo en un número primo $ m $.
3. Seleccionar un número primo grande como contraseña $ p $.
4. El mensaje codificado va a ser $ m' = m \cdot p $.

Y para decodificar el mensaje, los pasos son:

0. Obtener el mensaje cifrado $ m' $ y la contraseña $ p $.
1. El mensaje original es $ m = \frac{m'}{p} $.

Y listo! Tenemos el mensaje $m$ oculto detrás de el mensaje $m'$, utilizando la contraseña $p$. Para saber $m$ es necesario factorizar $m'$. ¿Qué tan difíciles factorizar $m'$? Y ¿Qué tan fácil es producir tantos números primos?

1. Como ya lo hemos dicho, factorización es un problema **muy difícil** en la ciencia de la computación. . Es decir que *para números grandes*, esta técnica es bastante segura.

2. Por otro lado, checar si un número es primo es un problema fácil de resolver. De hecho, en 2002 tres investigadores indios desarrollaron el [Test AKS](https://es.wikipedia.org/wiki/Test_de_primalidad_AKS), que verifica primalidad de manera muy eficiente. Esto significa que encontrar un número primo grande para nuestro mensaje es relativamente fácil, y por lo tanto **encriptar el mensaje es fácil**.

El algoritmo tiene un problema. Este problema no aparece cuando enviamos un solo mensaje, sino cuando enviamos **más de un mensaje**. ¿Alcanzas a ver cuál es?

Supongamos que enviamos el primer mensaje $m'_1 = m_1 \cdot p$, y luego queremos enviar otro mensaje $m'_2 = m_2 \cdot p$. Imaginemso que un atacante captura ambos mensajes cifrados $m'_1$ y $m'_2$. Si un atacante supiera $m'_1$ y $m'_2$, ¿podría obtener la contraseña $p$ o los mensajes?

Resulta que **sí**. Si $m'_1 = m_1 \cdot p$ y $m'_2 = m_2 \cdot p$, entonces $p$ es el **Máximo Común Divisor (MCD)** de $m'_1$ y $m'_2$. Y resulta que calcular el MCD de dos números es un problema *muy* sencillo, y puede ser resuelto con el [Algoritmo de Euclides](https://es.wikipedia.org/wiki/Algoritmo_de_Euclides), que mencionamos en la sección anterior. Entonces, si el atacante tiene *dos mensajes cifrados*, es muy fácil obtener la contraseña, y por lo tanto decifrar **todos** los mensajes.

Es decir que aunque nuestra estrategia es buena, no puede funcionar con varios mensajes ya que se vuelve muy sencillo para un atacante el descifrar cuál es la contraseña.

¿Cómo podemos crear un algoritmo donde éste no sea un problema?

## El algoritmo RSA
RSA es uno de los algoritmos de encripción más populares, y a diferencia del algoritmo anterior, tiene la particularidad de utilizar dos contraseñas! Una para encriptar y la otra para descifrar.

El algoritmo RSA fue desarrollado en [la segunda mitad de los 70s](https://es.wikipedia.org/wiki/RSA) por tres científicos en MIT (otra vez!). La idea de encripción de llave pública es un tanto más antigua, ya que fue desarrollada por [Diffie y Hellman](https://es.wikipedia.org/wiki/Diffie-Hellman) a principios de los 70s.

Con sistemas criptográficos de llave pública, existen dos contraseñas: Una pública y una privada (duh). La clave pública y la clave privada no pueden ser arbitrarias: Tienen que ser escogidas juntas con un procedimiento matemático. Para poder entender cómo, vamos a estudiar el último concepto que nos hacía falta: El Teorema de Euler.

### Entra el Teorema de Euler
El teorema de Euler es la base del algoritmo RSA, y es importante entenderlo antes de explicar el algoritmo. La idea es que si tenemos dos números $a$ y $n$ tal que son *coprimos* (es decir $a \perp n $), entonces:

\[ a^{\phi(n)} \equiv 1 \;\;  (\bmod n\;) \]

Donde $\phi{n}$ es conocida como la función $\phi$ *de Euler*, y para números primos, es: $ \phi{p} = p - 1 $. Para un producto de dos números primos $pq$, entonces $\phi(pq) = \phi(p) \phi(q)$.

* Si decimos que Un número es **coprimo** con otro, significa que no tienen factores comunes ([chéquense Wikipedia](https://es.wikipedia.org/wiki/N%C3%BAmeros_primos_entre_s%C3%AD)).

### Las claves
Una vez que conocemos el teorema de Euler y la $\phi$ de Euler, entonces podemos aprender el proceso para seleccionar las claves, que es el siguiente:

1. Seleccionar dos números primos $p$ y $q$ aleatoriamente, tal que $p \neq q$, y que ambos sean de 1024 bits (o sea, números bastante grandes).
2. Obtener $N = p \cdot q $. Le llamamos el 'módulo del sistema'.
3. Obtener $\phi(N) = (q-1)(p-1)$, y un número impar $e$ tal que $0<e<\phi(N)$, y tal que $e$ sea **coprimo** con $\phi(N)$. Los números $e$ y $N$ constituyen la clave pública de encripción $ku = {e, N} $.
4. **El paso de la muerte**: Obtener el número $d$ tal que $0 \leq d \leq n $ y $ ed \equiv 1 \;\; (\bmod \phi(n)) $. Para obtenerlo sólo es necesario intertar distintos valores de $ed$ y ver cuál cumple con las condiciones anteriores.
5. El par $P = (e,N)$ es la **clave pública**.
6. El par $S = (d,N)$ es la **clave privada**.

¿Agarraron la magia? Es posible que se les haya ido, pero utilizando el par público y el par privado es posible encriptar un mensaje $M$ que viene en forma de un número.

Pe-pero... si $N$ es público, ¿no podrían factorizarlo, obtener $p$, $q$ y los demás parámetros y ya? Resulta que para el algoritmo RSA, la clave pública es de 2048 bits. Éste es un número enorme. De hecho, tomaría *miles de años* factorizar un número de ese tamaño, así que: sí, nuestros secretos y nuestros códigos están seguros.

### Encriptando y desencriptando
Ahora sí vamos al punto. ¿Cómo podemos encriptar y desencriptar mensajes utilizando las claves pública y privada de RSA?

Pues es bastante fácil. Dado un mensaje $M$ y una llave pública con $e$ y $N$, el mensaje cifrado $C$ se calcula así:

\[ C = M^e \bmod N \]

Donde, como sabemos, $e$ y $N$ son los componentes de la clave pública. ¿Cómo descifrar el mensaje? Pues dado un mensaje $C$, y una llave privada que consiste de $d$ y $N$, entonces para descifrar el mensaje $M$:

\[ M' = C^d \bmod N \]

Y de hecho $M' = M$! : )

¿Qué? Es un poco confuso, pero así es como funciona el algoritmo. Si quieres ver porqué, checa la prueba en la siguiente sección. Si decides **creernos**, entonces es todo : ).

### Prueba que M' = M
Vamos a ver cómo es que ocurre esto. Recordemos que $ C = M ^e \bmod N $, por lo tanto:

\[ M = C ^ d \bmod N = (M^e \bmod N)^d \bmod d \]

De aquí obtenemos lo siguiente:

\[ M = M^{ed} \bmod N \]

Del paso **4** para obtener las claves, podemos recordar que $ ed \equiv 1 \;\; (\bmod \phi(n)) $, es decir que $ ed = 1 + \phi(N) \times k$, donde $k$ es un entero que no conocemos. ¿Todo bien? Es decir que:

\[ M = M^{1+\phi(N)\times k} \bmod N = M \cdot M^{\phi(N)\times k} \bmod N \]

Y ahora es donde usamos el **Teorema de Euler**. Recordemos que el teorema de Euler dice que & a^{\phi(n)} \equiv 1 \;\;  (\bmod n\;) $. Es decir que:

\[ M = M \cdot 1^{k} \bmod N = M \bmod N \]

Por lo tanto, si $M < N$, entonces $M \bmod N = $. Y así es como funciona el algoritmo RSA!!!

## Conclusión
Pues hemos dado un pequeño viaje matemático para entender cómo funciona uno de los algoritmos criptográficos más populares que existen.

Es un poquito difícil observar cómo es que todas las partes del algoritmo funcionan juntas. Vale la pena que te tomes unos minutos para descansar, y vuelvas a checar cómo se calculan las claves. El algoritmo es muy interesante una vez que puedes entenderlo completamente.

En los últimos años hemos escuchado mucho sobre la computación cuántica y cómo *se supone* que con ella, el mundo de la seguridad informática se iba a voltear de cabeza porque sería posible romper la barrera presentada por la encripción.

Si analizas el algoritmo un poco, puedes ver exactamente porqué el cómputo cuántico podría voltear al mundo de cabeza: El algoritmo RSA depende del hecho que el número $N$, que **es público**, es difícil de factorizar. Posteriormente hablaremos del **Algoritmo de Shor**, que es un algoritmo cuántico muy rápido para factorizar números, y que podría cambiar la criptografía para siempre.

Hasta la próxima! : )
