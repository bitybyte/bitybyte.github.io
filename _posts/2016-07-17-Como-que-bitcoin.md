---
layout: post
title: ¿Bitcoin?
author: Pablo Estrada
author_site: http://iampablo.me
tags:
  - español
  - cs
  - cryptography
  - programming
  
---
En los últimos años, hemos escuchado por aquí y por allá sobre criptomonedas. Al principio eran los bitcoins, y después fueron los dogecoins, coinye, y una [larga lista de ejemplos](https://en.wikipedia.org/wiki/List_of_cryptocurrencies). Estas monedas digitales se convirtieron en interesantes iniciativas que buscan crear sistemas monetarios que no dependan de bancos, gobiernos o entidades centralizadas.

Las criptomonedas son propuestas muy interestantes, y que para muchos han resultado en un área muy rentable a la cual dedicarse. Al rededor del mundo, y en especial en China, existen centros de datos con miles de computadoras, llamadas granjas, cuyo único propósito es "minar" criptomonedas, porque puede ser muy rentable.

Pero entonces ¿Qué es un bitcoin, y cómo es que funciona? ¿cómo es que tiene un valor monetario? Ahora vamos a discutirlo.

## Satoshi Nakamoto
En el año 2008, un investigador de nombre Satoshi Nakamoto publicó [un artículo científico](https://bitcoin.org/bitcoin.pdf) donde definía un sistema de dinero electrónico llamado BitCoin. El artículo es corto, y *no tan difícil* de leer.

Junto con ese artículo, Satoshi implementó [el sistema de Bitcoin](https://github.com/bitcoin/bitcoin/), y lo publicó como software libre para el mundo. Desde entonces, **nadie supo nada** más de Satoshi. No se sabe si de verdad se llama Satoshi, si es Japonés o no... de hecho, no se sabe si en verdad es una persona, o un grupo. Sin embargo, su idea fue muy importante, y muchos la utilizaron para crear un sistema monetario que hoy vive y transfiere miles de dólares al día.

¿Cómo imaginó Satoshi a Bitcoin? Pues con las siguientes cualidades en mente:

* Digital. El medio debería funcionar digitalmente, y por lo tanto sin fronteras. Un bitcoin sería representado por una cadena de bytes.
* Descentralizado. Satoshi quería un sistema para realizar intercambios monetarios que no requiriera una **autoridad central** (e.g. un banco, o un gobierno).
* Confiable. Dado que un bitcoin no es más que una cadena de bytes, necesitamos una manera de asegurar que una persona **no puede gastar la misma moneda dos veces**.

Aunque cada una de estas características suena razonable, si las consideramos en conjunto, entonces es bastante difícil. ¿Cómo asegurarnos de que na persona no *pague doble* si no hay un banco para verificar las transacciones? Pues con matemáticas

Para crear un sistema que tuviera esas tres cualidades, Satoshi utilizó bases de la criptografía de llave pública para definir el **blockchain**. Primero vamos a ver cómo se transifere un bitcoin.

### Antes que nada
Antes de continuar con la lectura, es importante que estés más o menos familiarizado con la criptografía de llave pública. En [éste post]( {{site.url}}/Algoritmo-RSA/ ) estudiamos el algoritmo RSA en detalle. El punto principal que debes entender de la criptografía de llave pública es:

* El par de llave pública y llave privada es un par de números **grandes** relacionados entre sí por una serie de reglas matemáticas. Gracias a su relación es posible **esconder** información utilizando una de las llaves, y **recuperar** la información utilizando **la otra llave**. **Nota**: A las llaves les podemos llamar **clave** o llave de igual manera.

* También es importante que sepas que una función hash. Una función hash es una función que permite *mapear* datos de tamaño arbitrario, a un dato de tamaño fijo. Es utilizado en [hash tables]( {{site.url}}/Hashtables); y es muy útil para verificar que un conjunto de datos **no está corrupto**. En pocas palabras, dada una cadena de bytes, su **hash** es un número pequeño que permite identificarla.

## Las transacciones
 Una moneda electrónica (o bitcoin) es una *cadena* de firmas digitales. Es decir que un bitcoin es, como ya dijimos, una cadena de bytes que representa una *firma electrónica* de algo.

Por ejemplo, supongamos que **yo** soy dueño de un bitcoin, que es representado por el número **2361**. Le quiero dar el bitcoin a mi amigo **Pancho**.

Cuando un usuario quiere transferirle una moneda de bitcoin a otro usuario, entonces tiene que **encriptar** el valor del bitcoin junto con la llave pública del siguiente dueño.

Por ejemplo, supongamos que la **llave pública de Pancho** es **32431**. Entonces, para enviar mi bitcoin (valor: 2361) a Pancho (clave pública: 32431), pongo ambos números juntos: **236132431**. Esto es como decir "bitcoin 2361 es del usuario 32431". Una vez juntos, utilizo **mi llave privada** para cifrar ese número y el resultado (por ejemplo **7493**) se lo mando a **Pancho**.

Dirás entonces: Si está cifrado, entonces ¿cómo sabe Pancho que le mandé mi bitcoin? Como ciframos el contenido con **mi clave privada**, entonces puede ser **descifrado** por Pancho utilizando **mi clave pública**. Al descifrarlo, Pancho va a poder ver su clave pública y un código de bitcoin, y verificar que sí se lo mandé a él.

¿Qué pasa cuando Pancho quiera darle ese BitCoin a un nuevo usuario? Pues pancho hace lo mismo: Tomar el bitcoin (valor: 7493), concatenarlo con la clave pública del usuario, *hashearlos*, y encriptarlos usando su clave privada. Así el nuevo usuario puede checar con la clave pública de **Pancho**.

La siguiente figura explica cómo funciona:

![Transaction chain]( {{site.url}}/images/transaction_chain.png )

Ahora tú te debes estar preguntando dos cosas: ¿**Qué evita** que **yo invente** números cualesquiera para ser mi BitCoin? y ¿Si todo son códigos digitales, **qué evita** que yo le mande el bitcoin **2361** a Pancho, y después se lo mande a **Andrés**? Eso lo vamos a explicar en la siguiente sección.

Por lo pronto, tómate dos minutos para descansar, y vuelve a leer la sección anterior. Es **muy importante** que la entiendas.

## Verificando las transacciones
Las dos preguntas que hicimos en la sección anterior tienen que ver con verificación de las transacciones. La solución más sencilla sería tener un servidor central, que valide ambas cosas: (1) el número que creaste **no** es un bitcoin, y (2) ese bitcoin **ya se lo diste a Pancho**, así que no se lo puedes dar a **Andrés**. Existe un **pero** para esta sugerencia.

Como ya lo dijimos anteriormente, Satoshi quería un sistema que no fuera centralizado y que no dependiera de una autoridad central. Para eso, Satoshi imaginó que *en vez de un banco* o un servidor central, habría una **red distribuída** que estaría a cargo de verificar transacciones. La red está formada de computadoras libres, esparcidas por el internet. ¿Cómo convencer a usuarios y empresas de participar verificando transacciones? Con incentivos: Al permitir que tu computadora participe en la red de bitcoin, puedes ganar bitcoins. Esto funciona a través del blockchain.


¿Qué es el **blockchain**, y el **proof-of-work**? Éstas son técnicas para crear una *autoridad distribuída*. El blockchain es una **cadena de bloques** muy similar a la cadena de hashes de transacciones. Cada cierto tiempo **un bloque nuevo** es agregado a la cadena, y *comunicado a toda la red*. Este bloque es calculado por una de las máquinas de la red, y a esta máquina se le paga un bitcoin por calcular el bloque. Un bloque es una **cadena larga** de bytes, que entre otras cosas incluye varias transacciones, un *hash* del bloque anterior, y un número especial llamado **nonce**.

Los bloques se van agregando **de uno en uno**, y cada bloque tiene un hash del bloque anterior. Esto significa que los bloques van formando una cadena. El siguiente diagrama muestra más o menos como funciona:

![Block chain]( {{site.url}}/images/block_chain.png )

La idea es que el **block chain** va a ir validando todas las transacciones. Una vez que una transacción ha sido agregada a un bloque, y **el siguiente bloque** ha sido agregado, entonces **no se puede cambiar la transacción**. ¿Porqué? Por que si quisiéramos cambiar una transacción en el bloque anterior, entonces **su hash code cambiaría**; y entonces **tendríamos que cambiar el bloque siguiente**. De hecho, cada bloque nuevo en la cadena, hace que las transacciones viejas sean **más y más** difíciles de cambiar.

Ahora tal vez venga a ustedes una pregunta: ¿Cómo evitar que una sola computadora agregue bloque tras bloque tras bloque y se "apodere" de la red? Aquí es donde entra el **proof of work**.

Satoshi decidió limitar la velocidad a la que los bloques son agregados a la cadena. Para esto, diseñó un problema algorítmico **difícil** que las máquinas de la red de bitcoin deben resolver. **La primera máquina de la red en resolver este problema es la primera en obtener el nuevo bloque, y publicarlo a la red.** A esta máquina se le paga **un bitcoin** por su trabajo.

Una vez que un nuevo bloque es publicado, entonces **el problema algorítmico** cambia, y la red empieza a **generar el nuevo bloque**.

Éste es el núcleo de bitcoin. Si entiendes esta sección y la sección anterior, entonces **entiendes cómo funciona bitcoin**. No te desesperes si todavía no: Toma tiempo.

## Problems in paradise
¿Recuerdan que les dije que el número de bloques que entran al sistema es limitado? Así es. Una cosa más: El tamaño del bloque **también** es limitado (un bloque es de 1 MB). ¿Te das cuenta de lo que significa?

Una transacción es una **cadena de bytes**. Y un bloque **contiene varias transacciones**. Esto significa que un bloque puede contener un **número limitado** de transacciones, y como los bloques están limitados, esto significa que **el sistema tiene un límite máximo de transacciones por segundo**. Y **nos estamos acercando**.

Desde el inicio de bitcoin, el número de transacciones ha ido creciendo **exponencialmente**. La siguiente figura cuenta el número de transacciones por mes:

![Bitcoin transactions]( {{site.url}}/images/bitcoin_txns.png )
Transacciones por mes en bitcoin (Escala logarítmica)

Este problema ya se ha manifestado en Bitcoin. Cada vez las transacciones toman más tiempo en ocurrir, y en algunos casos las transacciones son *tantas*, que no pueden ser validadas por el sistema y algunas son ignoradas.

Algunos desarrolladores han propuesto que el tamaño del bloque debe ser incrementado a más de 1MB, pero a menos que **la mayoría de la red** esté de acuerdo, es imposible cambiar el sistema - y la mayoría de la red parece contenta corriendo al acantilado.

Esta historia está ocurriendo, y es emocionante e interesante: ¿Podrá bitcoin salvarse de su propia destrucción? ¿O se volverá un sistema mediocre y bajo el control de algunos pocos?

Si te interesa saber más sobre la lucha interna por rescatar bitcoin [checa éste artículo](https://www.technologyreview.es/informatica/48118/todavia-desaconsejo-invertir-los-ahorros-en/).

## Conclusión
Hemos estudiado en general cómo funciona una criptomoneda, y en particular bitcoin. El problema es interesantísimo, y la solución es brillante. Si tienes más curiosidad, puedes leer [el artículo de Satoshi](https://bitcoin.org/bitcoin.pdf), que explica cómo funciona el sistema de una manera más o menos sencilla (y en inglés).

Bitcoin es un sistema complejo e interesante; del cual se puede hablar mucho. En este post no hablamos del negocio de *mining*, y si vale la pena o no. Tampoco hablamos de las guerras políticas en la red, pero también es un tema interesante ; )

En la siguiente sección, si te interesa, vamos a comentar del problema algorítmico diseñado por Satoshi para controlar el número de bloques. Para leer la siguiente sección, tienes que entender muy bien hashing.

## Extra: El problema algorítmico
Para aquellos que quieran ver más adentro de bitcoin, vamos a ver cuál es el problema algorítmico que Satoshi diseñó para limitar el número de bloques: A esto Satoshi le llamó **Proof-of-Work**, y tiene que ver con el número **nonce** que es incluído como parte de un bloque.

La idea es simple: Encontrar un **nonce** tal que al obtener el **hash** del bloque entero, haya un cierto **número de ceros** a la derecha.

Es decir: Una función de hash genera números que parecen ser aleatorios, pero que *representan* un conjunto más grande de bytes. El problema propuesto por Satoshi es **buscar** un número dentro de un pajar.

Las ventajas de este problema es que si algunos bloques son encontrados muy rápidamente, puede ser ajustado al **incrementar el número de ceros a la derecha** que se necesitan para el hash de un bloque.

Otra ventaja de agregar este problema algorítmico es que ayuda a **democratizar** la red: Una computadora es un voto. Aquél que controle el **mayor poder de cómputo** puede controlar a la red. Para saber más, [checa el paper](https://bitcoin.org/bitcoin.pdf)!
