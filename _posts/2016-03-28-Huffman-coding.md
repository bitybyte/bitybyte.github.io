---
layout: post
title: Comprimiendo datos - el algoritmo de Huffman en Python
author: Pablo Estrada
author_site: http://iampablo.me
tags:
  - español
  - cs
  - compression
  - programming
  
---
Hoy vamos a platicar sobre cómo hacer un pequeño programa para comprimir datos de un archivo de texto. Nuestro programa va a funcional con un algoritmo de compresión bastante famoso: El algoritmo de Huffman. Con él vamos a utilizar **menos bits** para guardar la misma cantidad de información.

La idea es muy simple: En un archivo normal, todos los símbolos son de un byte (8 bits). En nuestro archivo comprimido, los símbolos que se utilizan mucho pueden ser codificados *con menos bits*, y los símbolos que casi no se usan pueden ser codificados *con más bits*, y así, el promedio de *bits por símbolo* sería *menor a 8*.

## Historia
Pero antes de platicar del algoritmo, vamos a platicar de la cadena de ocurrencias que llevaron a que el algoritmo de Huffman fuera descubierto. (**Si esta parte no te interesa**, puedes saltártela e ir directo a **El algoritmo**)

Todo empieza en los 40s, en Bell Labs, un laboratorio de investigación en Nueva Jersey, Estados Unidos. Tal vez recuerden Bell Labs de alguna clase, porque un sinnúmero de invenciones y avances tecnológicos ocurrieron ahí: El láser, el transistor, el sistema operativo Unix, los lenguajes de programación C y C++, etc.

Un joven investigador llamado Claude Shannon trabajaba como criptoanalista en Bell Labs. Entre su investigación hubo un artículo llamado *A Mathematical Theory of Communication* (en español: Una Teoría Matemática de Comunicaciones). Este artículo define varios conceptos, como *entropía*, y *cantidad de información*. La teoría presentada por Shannon en su artículo se convirtió en la Teoría de la Información, que es la base teórica de la compresión de datos!

Shannon trabajó con Robert Fano, un profesor en MIT, que eventualmente empezó a enseñar una clase de Teoría de la Información.

Entra David Huffman! En 1951, Huffman tomó la clase de Teoría de la Información del profesor Fano. El proyecto final de la clase era diseñar un algoritmo que permitiera obtener códigos *óptimos*. Y el resto es historia!

Por cierto, todos los nombres y lugares mencionados son gente y lugares interesantísimos. Si tienen curiosidad: [Shannon](https://es.wikipedia.org/wiki/Claude_Elwood_Shannon), [Fano](https://en.wikipedia.org/wiki/Robert_Fano), [Huffman](https://es.wikipedia.org/wiki/David_A._Huffman), [MIT](https://es.wikipedia.org/wiki/Instituto_Tecnol%C3%B3gico_de_Massachusetts), [Bell Labs](https://en.wikipedia.org/wiki/Bell_Labs).

## El algoritmo
El algoritmo de Huffman recibe una distribución de probabilidad de los símbolos del archivo. Basado en esta distribución, se crea un árbol binario de codificación utilizando una *fila de prioridad*, y con ese árbol de codificación se genera un diccionario. Luego ese diccionario es utilizado para codificar el archivo. Vamos a ver por partes:

#### Obteniendo la distribución
La distribución de entrada define las probabilidades de que cada símbolo aparezca en el archivo, por ejemplo, un archivo con 50% de la letra 'a', 30% de 'b' y 20% de 'c' tiene la siguiente distribución: $P(a) = 0.5$, $P(b)=0.3$, $P(c)=0.2$.

Para calcular la distribución de un archivo utilizamos la siguiente función:

{% highlight python %}
def get_probabilities(content):
    total = len(content) + 1 # Agregamos uno por el caracter FINAL
    c = Counter(content)
    res = {}
    for char,count in c.items():
        res[char] = float(count)/total
    res['end'] = 1.0/total
    return res
{% endhighlight %}

Se darán cuenta de que estoy agregando el símbolo `'end'` a las probabilidades. Ése es un pequeño truco que nos servirá posteriormente para descomprimir el archivo.

Entonces, un ejemplo del resultado nuestra función sería `get_probabilities("aaaabbbcc") -> {'a':0.4,'b':0.3,'c':0.2,'end':0.1}`. Fíjense que el resultado es simplemente una tabla:

|Simbolo|Probabilidad|
|a|0.4|
|b|0.3|
|c|0.2|
|Fin|0.1|

#### Obteniendo el árbol de codificación
El siguiente paso es obtener el árbol de codificación. Éste es *el paso de la muerte* del algoritmo de Huffman, y consiste en los siguientes pasos:

1. Poner todos los símbolos en una fila de prioridad de acuerdo a sus probabilidades.
2. Combinar los dos símbolos **menos probables** en un solo nodo de un árbol.
3. Insertar el nuevo nodo a la fila de prioridad
4. Repetir desde el paso 2 hasta que sólo haya **un elemento** en la fila de prioridad.

Para nuestra fila de prioridad vamos a utilizar un módulo de Python llamado **heapq**, que provee una fila de prioridad basada en Heaps. La fila de prioridad según nuestro ejemplo se ve así:

<center><img src="http://bitybyte.github.io/images/huffpriq1.png"></img><br>
Figura 1. Dados los datos de entrada, la fila de prioridad tiene esta forma.
</center>

{% highlight python %}
def make_tree(probs):
    q = []
    # Agregamos todos los símbolos a la pila
    for ch,pr in probs.items():
        # La fila de prioridad está ordenada por
        # prioridad y profundidad
        heapq.heappush(q,(pr,0,ch))

    # Empezamos a mezclar símbolos juntos
    # hasta que la fila tenga un elemento
    while len(q) > 1:
        e1 = heapq.heappop(q) # El símbolo menos probable
        e2 = heapq.heappop(q) # El segundo menos probable
        
        # Este nuevo nodo tiene probabilidad e1[0]+e2[0]
        # y profundidad mayor al nuevo nodo
        nw_e = (e1[0]+e2[0],max(e1[1],e2[1])+1,[e1,e2])
        heapq.heappush(q,nw_e)
    return q[0] # Devolvemos el arbol sin la fila
{% endhighlight %}

Vamos a ver cómo funcionaría la rutina `make_tree` con los datos que recibimos de entrada:

<center><img src="http://bitybyte.github.io/images/huffpriq2.png"></img><br>
Figura 2. Los pasos para construir el árbol de codificación partiendo de la cola en la Figura 1.</center>

El resultado final puede ser visto en la figura 3. Para ver el proceso paso por paso puedes [hacer click aquí](http://bitybyte.github.io/images/huffpriqfinal.png).

<center><img src="http://bitybyte.github.io/images/huffpriq3.png"></img><br>
Figura 3. Resultado tras combinar todos los nodos en la fila de prioridad y generar un árbol.</center>

#### Creando el diccionario de codificación
Una vez que tenemos un árbol de codificación, tenemos que usarlo para generar un diccionario de codificación. Para esto tenemos que generar códigos binarios para cada uno de los símbolos del archivo de entrada. ¿Cómo generamos los códigos binarios utilizando el árbol del algoritmo?

Exactamente. Marcamos cada *rama* del árbol con 1 o 0! Y entonces el código de cada símbolo será la serie de 1s y 0s que hay que atravesar para alcanzarlo.

<center><img src="http://bitybyte.github.io/images/huffcodemk.png"></img><br>
Figura 4. Códigos binarios para los símbolos de nuestro ejemplo.</center>

Para conseguir esto, vamos a hacer DFS (Depth-First Search) sobre el árbol, generando los códigos conforme 'bajamos' por las ramas. 

{% highlight python %}
def make_dictionary(tree):
    res = {} # La estructura que vamos a devolver
    search_stack = [] # Pila para DFS
    # El último elemento de la lista es el prefijo!
    search_stack.append(tree+("",)) 
    while len(search_stack) > 0:
        elm = search_stack.pop()
        if type(elm[2]) == list:
            # En este caso, el nodo NO es una hoja del árbol,
            # es decir que tiene nodos hijos
            
            # El hijo izquierdo tiene "0" en el prefijo
            search_stack.append(elm[2][1]+(prefix+"0",))
            # El hijo derecho tiene "1" en el prefijo
            search_stack.append(elm[2][0]+(prefix+"1",))
            continue
        else:
            # El nodo es una hoja del árbol, así que
            # obtenemos el código completo y lo agregamos
            code = elm[-1]
            res[elm[2]] = code
        pass
    return res
{% endhighlight %}

Con la rutina `make_dictionary` obtenemos el diccionario de codificación, y tenemos todo lo necesario para codificar nuestro archivo a su versión comprimida:

#### Codificando el texto
Ya que tenemos el diccionario, codificar el texto es la parte más fácil. Simplemente tenemos que concatenar todos los bits de cada uno de los símbolos. Esto nos va a generar una cadena bien larga.

Por ejemplo, dado el **diccionario en la Figura 4**, y la cadena de entrada `"aaaabbbcc"`, el resultado sería `"0 0 0 0 10 10 10 111 111 110"` (como ven `a:0`, `b:10`, `c:111`, `Fin:110`). Ya todo junto sería: `"0000101010111111110"`.

Pero no queremos una variable de tipo `String`. Queremos una cadena de **bits**. Así que tenemos que convertirlo a entero. Esto nos hace descubrir un problema. Los ceros a la izquierda!

$$
0000101010111111110_2 = 101010111111110_2
$$

Para arreglar este problema, vamos a agregar un 1 a la izquierda, para mantener la cuenta de ceros. Es decir que nuestra cadena `"aaaabbbcc"` va a quedar codificada así: `"10000101010111111110"`. Con el 1 a la izquierda podemos convertirla a entero y no perder los ceros! : )

Afortunadamente, en Python las variables enteras no tienen un límite para su longitud (Hurra por los lenguajes de alto nivel!). La función se ve así:

{% highlight python %}
def compress(dic,content):
    res = ""
    # Iteramos sobre cada elemento del archivo de entrada
    for ch in content:
        code = dic[ch]
        res = res + code
    # Agregamos el 1 a la izquierda, y el marcador de final
    # a la derecha
    res = '1' + res + dic['end']
    # Agregamos ceros para que la longitud del resultado
    # sea un múltiplo de 8
    res = res + (len(res) % 8 * "0")
    return int(res,2) # Convertimos a entero! (2 porque es base 2)
{% endhighlight %}

Y con esto tenemos todo listo! Ahora sólo es necesario guardar la cadena de bits en forma de *int* que viene de `compress`, y el diccionario; y tenemos todo lo necesario para decodificar el archivo!

#### Guardando a disco
Para guardar a disco, vamos a hacerlo muy simple. El diccionario vamos a guardarlo como está en JSON, y para la cadena de bits vamos a utilizar una librería estándar de Python llamada pickle, que está diseñada para salida y entrada binarias. La función es simple:

{% highlight python %}
def store(data,dic,outfile):
    # Guardamos la cadena de bits en un archivo, que abrimos
    # en modo binario (por eso 'wb')
    outf = open(outfile,'wb')
    pickle.dump(compressed,outf)
    outf.close()

    # Guardamos el diccionario en otro archivo en formato JSON
    outf = open(outfile+".dic",'w')
    json.dump(dic,outf)
    outf.close()
    pass
{% endhighlight %}

#### Poniéndolo todo junto
Pues ya que tenemos todas las partes del algoritmo, podemos ponerlas juntas:

{% highlight python %}
    # Leemos el archivo de entrada completo a cont
    inf = open(sys.argv[1])
    cont = inf.read()
    inf.close()
    # Calculamos la distribución de probabilidad para cada símbolo
    probs = get_probabilities(cont)
    # Construimos el árbol de parseo! : )
    tree = make_tree(probs)
    # Construimos el diccionario para codificar
    dic = make_dictionary(tree)
    # Codificamos el contenido del archivo
    compressed = compress(dic,cont)
    # Guardamos todo en disco!
    store(compressed,dic,sys.argv[2])

    print("Archivo comprimido!")
{% endhighlight %}

Y listo, con esto tenemos el programa completo para comprimir archivos. Fíjense que si la cadena `"aaaabbbcc"`, que es de 9 bytes (72 bits) queda codificada como `"10000101010111111110"` (20 bits), entonces estamos comprimiendo más del 70%!

## Conclusión
El algoritmo de Huffman es bastante sencillo, pero es brillante e importantísimo. Les recomiendo que traten de implementarlo por su cuenta (o que estudien el código de esta implementación)!

Ya revisamos las partes que integran un encoder que utiliza el algoritmo de Huffman. El script del encoder, un archivo de prueba (lipsum.txt) y su versión comprimida (lipsum.cmp) con su diccionario de decodificación (lipsum.cmp.dic) están [disponibles en este Gist](https://gist.github.com/pabloem/7974e7a1dc755190b7f1). Funcionan con Python 3! Pueden checar qué tanto se comprimen distintos archivos.

Para correr el programa pueden usar el siguiente comando:
{% highlight bash %}
$> python3 huffman_en.py lipsum.txt test
{% endhighlight %}

O el siguiente:

{% highlight bash %}
$> ./huffman_en.py lipsum.txt test
{% endhighlight %}

Como habrán visto, no he posteado el programa para **descomprimir** o decodificar la información. ¿Hay algún valiente que se atreva a codificarlo?
