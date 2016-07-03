---
layout: post
title: 3 preguntas comunes en una entrevista de programación
author: Pablo Estrada
author_site: http://iampablo.me
tags:
  - español
  - cs
  - programming
  
---
*Software is eating the world*, dicen muchos. En los últimos años, la industria del software ha vivido una increíble explosión en tamaño, ventas y atención de medios. El mundo hispanohablante no es la excepción, y en especial México, que se ha convertido en una fuente de talento para Silicon Valley.

Sin embargo, aunque las universidades en México han sabido educar en matemáticas y ciencia de la computación, no han sabido preparar a sus estudiantes para el paso más importante de la salida al mundo real: La entrevista clásica de programación.

En este post vamos a estudiar 3 preguntas comunes en entrevistas de programación. Todas las preguntas son bastante sencillas. Vamos a resolverlas, y estudiar sus puntos importantes.

## 1. Insertar un nodo a una lista ordenada
Esta pregunta es clásica, y bastante sencilla. La respuesta es simple: Recorrer la lista, hasta encontrar en dónde colocar el nuevo nodo, y ahí lo colocamos. Esta pregunta permite checar habilidades básicas de algoritmos, pero también permite checar que puedas
checar **casos especiales**. Es muy fácil saltarse algunos detalles, y por eso es importante ; ).

Primero que nada, el nodo de nuestra lista es algo así:

{% highlight c %}
struct node {
    struct node* next;
    int value;
}
{% endhighlight %}

El prototipo de nuestra función va a ser entonces: `struct node* insert_ordered(struct node* new_node, struct node* head)`. Recibe la 'cabeza' de una lista, y un nodo con el nuevo valor que queremos insertar; y regresa la cabeza de la lista con el nodo agregado.

Por ejemplo, si tenemos una lista `1->2->6->8` e insertamosel número 5, el resultado debe ser: `1->2->5->6->8`. Si a eso le insertamos el número 0, el resultado debe ser `0->1->2->5->6->8`. Va que va? Pues antes de leer
la implementación, te invito a implementarlo tú mismo, y checa:

{% highlight c %}
struct node* insert_ordered(struct node* new_node, struct node* head) {
    // Lo primero que hay que hacer es checar los argumentos de 
    // entrada y los casos especiales. Esto es el catch de la pregunta
    if(new_node == NULL) return head; // Esta condición es un error.

    // Si la lista no tiene nada, entonces el nuevo nodo es la cabeza de la lista
    if(head == NULL) return new_node;
    // Particularmente, el caso donde la cabeza de la lista debe cambiar
    // porque el nuevo valor es menor
    if(head->value > new_node->value) {
        new_node->next = head;
        return new_node;
    }
    // Finalmente, el ciclo para encontrar el "caso normal", 
    // donde buscamos el espacio para insertar el nuevo nodo
    struct node* currNode = head->next;
    struct node* prevNode = head;
    while(currNode != NULL) {
        if(currNode->value > new_node->value) {
            prevNode->next = new_node;
            new_node->next = currNode;
            return head;
	}
    }
    // Finalmente, si TODOS los elementos de la lista son MENORES,
    // entonces lo insertamos al final : )
    prevNode->next = new_node;
    new_node->next = NULL;
    return head;
}
{% endhighlight %}

Esta pregunta es importante y muy útil. Con ella, el entrevistador puede verificar que el candidato conoce cómo funciona C, y la arquitectura interna de una lista ligada.

Además, puede que al entrevistador le interese checar cómo andas en análisis de complejidad. Sabes cuál es la complejidad de tiempo de ejecución para inserción en lista ligada ordenada? Sí, es $ O(n) $.

## 2. Encontrar si una cadena de caracteres contiene a otra
Este problema es más complicado, pero no es difícil. Vamos a hacerlo una vez más en C, pero también podríamos hacerlo en Java o cualquier otro lenguaje. Para hacerlo un poco más sencillo, vamos a asumir que la función tiene este prototipo: `int containsSubstring(char* mainStr, int mainStrLen, char* subStr, int subStrLen)`. Es decir que recibe punteros a las cadenas, y las longitudes de ambas cadenas, y debe devolver 1 si `subStr` es contenida en `mainStr`.

Por ejemplo, si `mainStr = "anita lava la tina"`, y `subStr = "la tina"`, `containsSubstring` devuelve `1`. Si `mainStr = "esternocleidomastorideo"` y `subStr = "equis"`, entonces `containsSubstring` devuelve `0`. ¿Quedó claro?

¿Cómo lo implementarías? (antes de checar la solución, trata de imaginarlo)

{% highlight c %}
int containsSubstring(char* mainStr, int mainStrLen, char* subStr, int subStrLen) {
    for(int i = 0; i < mainStrLen - subStrLen; i++) {
        for(int j = 0; j < subStrLen; j++) {
            // Si son diferentes, entonces
            if(mainStr[i+j] != subStr[j]) break;
            // Si hemos llegado al final, entonces hay match!
            if(j == subStrLen - 1) return 1;
        }
    }
    // Si llegamos aquí, entonces no la encontramos
    return 0;
}
{% endhighlight %}

Esta rutina es muy simple (tal que ocupa sólo un par de líneas!), y si la implementas en tu entrevista te iría bien. Sólo hace falta que obtengas la complejidad de ejecución de la función. ¿Puedes?

Vamos a decir que `mainStr` tiene longitud `n`, y `subStr` tiene longitud `m`. ¿Cuántas veces se ejecuta el ciclo *interior* en el peor caso? Un total de `m - 1` veces. ¿Qué tal el ciclo *exterior*? ¿Cuántas veces se ejecuta en el peor caso? Un total de `n - m` veces. Entonces, la complejidad es $O((m - 1)(n - m))$, o para más simple $O(nm)$. ¿Va?

Finalmente, aunque esta respuesta es suficiente para una entrevista, seguro te da más puntos el mencionar el [algoritmo de Knuth-Morris-Pratt](https://es.wikipedia.org/wiki/Algoritmo_Knuth-Morris-Pratt), desarrollado por los renombradísimos profesores Knuth, Morris y Pratt en Stanford y Carnegie Mellon. Este algoritmo tiene complejidad de $O(n+m)$! : )

## 3. Verificar si un árbol binario de búsqueda es válido

Este problema es interesante porque incluye recorrer un árbol, y además realizar una verificación al recorrerlo. Además puede ser un poco anti-intuitivo.

Para variar, Vamos a implementar este problema en Java. Vamos a codificarlo como una rutina que le pertenece a la clase `TreeNode`, la cual le permite validar a su propio sub-árbol. Vamos a decir que la clase `TreeNode` tiene los siguientes miembros:

{% highlight java %}
class TreeNode {
    TreeNode leftSon;
    TreeNode rightSon;
    Integer value;
    public boolean validate(){...}
    protected boolean validate(Integer min, Integer max){...}
}
{% endhighlight %}

Y nosotros vamos a implementar `public boolean validate()`, y `protected boolean validate(Integer,Integer)`. ¿Va?

Antes de codificarlo, vamos a estudiar bien la pregunta. Vamos a ver cómo se ve un árbol de búsqueda binario que es **válido**:

![Árbol válido]({{ site.url }}/images/binseachtree_valid.png)

Figura 1. Un árbol binario de búsqueda que **es válido**.

Vamos a fijarnos bien en el árbol de la Figura 1. Como pueden ver, para todos los nodos, sus hijos izquierdos contienen valores menores, y sus hijos derechos contienen valores mayores. Es posible encontrar cualquier número en este árbol. Chéquenlo bien.

Ahora vamos a ver un árbol que es inválido. Vamos a checar la siguiente figura:

![Árbol inválido]({{ site.url }}/images/binseachtree_invalid.png)

Figura 2. Un árbol binario de búsqueda que **es inválido**.

El árbol de la figura 2 no es válido. ¿Puedes explicar porqué? Todos los nodos tienen un hijo izquierdo que es menor, y un hijo derecho que es mayor, pero el número 10 no puede ser encontrado.

Con estos dos ejemplos en mente, ¿puedes codificar las rutinas `validate`? Intenta!

Nuestra implementación es así:

{% highlight java %}
public boolean validate() { return validate(null,null); }
public boolean validate(Integer min, Integer max) {
    // Si el valor es menor al minimo, entonces es un error
    if(min != null && value.compareTo(min) <= 0) return false;
    // Si el valor es mayor al maximo, entonces es un error
    if(max != null && value.compareTo(max) >= 0) return false;
    // Si no tenemos hijos, entonces los hijos son validos
    // Si sí tenemos hijos, entonces hay que verificarlos
    return ((leftSon == null ?
                 true : leftSon.validate(min, value)) &&
            (rightSon == null ?
                 true : rightSon.validate(value, max)));
}
{% endhighlight %}

La idea es simple: Llevar cuenta del máximo y el mínimo **globales** que un nodo puede contener. Basado en eso, checamos que el nodo no contenga valores *menores al mínimo* ni *mayores al máximo*, y si recorremos todo el árbol así, entonces sabremos que el árbol es 100% válido!

# Concluyendo
Bueno, pues visitamos 3 preguntas que a mí me han preguntado antes en entrevistas con empresas globales de software. Cuando estés en una entrevista es importante que no te pongas nervioso y platiques con tu entrevistador, **guiándolo en tu proceso**. Cuando interactúas con el entrevistador, él o ella puede tener una idea de cómo piensas a la hora de desarrolalr, y pueden ayudarte con pistas.

Si te interesa preparar más para entrevistas de programación, te recomiendo el libro [Cracking the Coding Interview](https://www.amazon.com.mx/Cracking-Coding-Interview-Programming-Questions/dp/0984782850/), que es probablemente el mejor libro para preparar entrevistas con empresas de software.

Cualquier duda o comentario, son todos bienvenidos : )
