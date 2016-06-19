---
layout: post
title: Decodificando el algoritmo de Huffman
author: Pablo Estrada
author_site: http://iampablo.me
tags:
  - español
  - cs
  - compression
  - programming

---
En un [post anterior](http://bitybyte.github.io/Huffman-coding/) aprendimos cómo comprimir datos utilizando el algoritmo de Huffman,
que es un algoritmo que permite obtener códigos óptimos para un conjunto de bits. En ese mismo post publicamos
un programa en Python que dado un archivo de texto, genera dos archivos: Un diccionario en JSON, y un archivo
binario que contiene los datos comprimidos del archivo de entrada. Sin embargo, no publicamos el programa
para descomprimir datos.

Pues hoy vamos a retomar el algoritmo y codificar rápidamente un script para generar un archivo descomprimido
a partir de un diccionario, y un archivo binario.

## Recordando
El algoritmo de Huffman recibe una distribución de probabilidad para una serie de símbolos. Por ejemplo, dada la
cadena de caracteres `"aaaabbbcc"`, para aplicar el algoritmo de Huffman es necesario obtener la probabilidad
de que cada símbolo occura. En este caso, la distribución de probabilidad sería: `{'a':4/9,'b':3/9,'c':2/9}`.

Con esto tendríamos suficiente para decodificar la cadena, pero en nuestro diseño del algoritmo de codificación
agregamos también un caracter de terminación, para saber cuándo dejar de decodificar. Esto quiere decir que además
de poder representar los caracteres `'a'`, `'b'` y `'c'`, tenemos que representar `end`, y la cadena sería
`"aaaabbbcc"+end`, y la distribución de probabilidad sería `{'a':4/10,'b':3/10,'c':2/10, end:1/10}`.

Después, con esa distribución creamos un árbol de codificación. Para recordar todo, revisa [el post](http://bitybyte.github.io/Huffman-coding/).

El chiste es que tenemos dos archivos: Un archivo binario, que no es más que una lista de bits que **empieza con 1**: `10010110100001`,
y un diccionario que permite traducir de cadenas de bits a caracteres, por ejemplo: `{'a':0, 'b':10, 'c':110, end:111}`. El archivo
binario empieza con 1 porque necesitábamos hacer un truquito a la hora de manipular **ceros a la izquierda**. De nuevo, si no te
acuerdas, porfa checa [el post donde explicamos Huffman coding](http://bitybyte.github.io/Huffman-coding/).


## Decodificando
Pues resulta que decodificar es mucho más fácil que codificar. Sólo es necesario seguir los siguientes pasos:

1. Cargar el diccionario a la memoria
2. Empezar a leer bits del archivo binario (saltarse el primer 1).
3. Leer bits de uno en uno, y checar si la cadena de bits está en el diccionario
4. Si encontramos la cadena de bits en el diccionario, escribir al archivo de salida
5. Si es el final, terminar. Si no, ir al paso 3.

Como ven, es bastante simple: Leer bits, checar en el diccionario y si es el final, terminar.

### Un ejemplo
Antes de presentar código, vamos a hacer un ejemplo rápido. Imaginemos que tenemos un archivo binario
que contiene `"10000101010111111110"`, y un diccionario que dice `{'a':0, 'b':10, 'c':111, Fin:110}`. Empezamos:

1. Cargamos el diccionario: `dic = {'a':0, 'b':10, 'c':111, Fin:110}`
2. Nos saltamos el primer bit. Entrada: `"0000101010111111110"`
3. Leemos un bit, y checamos en el diccionario. Bits leídos: `"0"`. Entrada: `"000101010111111110"`
4. Está 'bits leídos' disponible en el diccionario? Sí: `'a':0`. Salida: `a`.
5. Los siguientes tres bits son `"000"`, o tres `a`. Salida: `aaaa`. Entrada: `"101010111111110"`
6. Leemos un bit. Bits leídos: `"1"`. Entrada: `"01010111111110"`. No está en el diccionario.
7. Leemos otro bit. Bits leídos `"10"`. Entrada: `"1010111111110"`. Diccionario: `'b':10`. Salida: `aaaab`.
8. Los siguientes cuatro bits son `"1010"`, o dos `b`. Salida: `aaaabbb`. Entrada: `"111111110"`

Y de continuar así, es posible ver que los bits que quedan en la entrada `"111111110"` corresponden a `cc Fin`.

Siguiendo esta estrategia, podemos decodificar un archivo. Vamos a ponerlo en Python.

### El programa

Nuestro programa recibe un diccionario, y una cadena de bits. Nuestra rutina para decodificar es:

{% highlight python %}
# Recibe el diccionario y una cadena de bits
def decode(dic, bitstr):
    res = []
    length = bitstr.bit_length() - 1
    # El primer caracter debe ser 1, o hay un error
    if bitstr >> length != 1:
        raise Error("Corrupt file!")
    done = False
    # Iteramos hasta llegar al final
    while length > 0 and not done:
        shift = length - 1
        # Incrementamos de un bit en un bit
        while True:
            num = bitstr >> shift
            # Quitamos el 1 inicial y el 0b de formato
            bitnum = bin(num)[3:]
            if bitnum not in dic:
                shift -= 1
                continue
            char = dic[bitnum]
            if char == 'end':
                done = True
                break
            res.append(char)
            bitstr = bitstr - ((num - 1) << shift)
            length = shift
    return ''.join(res)
{% endhighlight %}

Para descargar el codificador, el decodificador y un par de archivos de muestra,
puedes ir a [este Gist](https://gist.github.com/pabloem/7974e7a1dc755190b7f1). Si tienes
preguntas, puedes comentar o enviaras a traves de Facebook ; ).

Suerte!
