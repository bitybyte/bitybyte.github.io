---
layout: post
title: Modelos de programacion - JavaScript - Asincrono y orientado a eventos
author: Pablo Estrada
author_site: http://iampablo.me
tags:
  - español
  - cs
  - programming
  
---
Éste es el primer post en una serie de post sobre *modelos de programación*. En esta serie vamos a estudiar algunos lenguajes de programación interesantes, y cómo funcionan sus modelos de ejecución. En esta ocasión vamos a hablar de JavaScript.

JavaScript es un lenguaje ligero, e interpretado, mejor conocido como *el lenguaje de la web*. La primera versión de JavaScript fue desarrollada en 1995 por [Brendan Eich](https://es.wikipedia.org/wiki/Brendan_Eich), cuando trabajaba para Netscape. En los últimos años, JavaScript se ha vuelto muy popular no sólo por su uso en sitios web, sino también por su uso en aplicaciones de *backend*. 

¿Porqué es interesante aprender sobre JavaScript? Pues de hecho, JavaScript y la plataforma Node.js se han convertido en uno de los ambientes más vibrantes y activosñ pero hoy vamos a pensar en JavaScript desde el punto ñoño y científico. Y la pregunta es entonces: ¿Qué tiene el modelo de programación de JavaScript de interesante? Los puntos principales - y las razones por als que escribo este post son:

* Las funciones son **objetos de primer nivel**
* Programación asíncrona
* Programación orientada a eventos
* Paralelismo en un solo hilo (qué?!)

Para entender a qué nos referimos con todo esto, vamos a hablar de la arquitectura básica de JavaScript.

## El loop de Eventos
JavaScript fue diseñado como un lenguaje para la interfaz de usuario. La interacción con usuarios normalmente es basada en eventos tales como clicks de mouse, y acciones en el teclado, ante los cuales se puede realizar una acción o rutina. Esta necesidad de responder a acciones de usuarios ayudó a que JavaScript fuera un lenguaje **basado en eventos**. Si alguna vez haz programado interfaces gráficas, seguramente ya tienes cierta idea. El modelo es simple:


![Event Loop](http://bitybyte.github.io/images/jseventloop.png)

Figura 1. El loop de eventos de JavaScript. En la izquierda está la fila de eventos. Cada evento es atendido de uno en uno por el hilo principal. Cada llamada es *ejecutada hasta ser terminada*.

JavaScript cuenta con una fila de eventos, la cual contiene *mensajes* que representan algún evento (por ejemplo: un click de un usuario, un error en una rutina, etc.). El **hilo principal** toma mensajes del frente de la fila de eventos, y los ejecuta completamente. Cada que un evento ocurre, un mensaje es agregado a la fila de eventos para que el hilo principal ejecute la acción correspondiente para ese evento. La acción correspondiente normalmente es una función definida por el programador. Por ejemplo, vamos a ver al siguiente código:

{% highlight javascript %}
request('http://www.google.com',
        function gotResponse(error, response, body) {
            console.log(body);
        });
console.log("Waiting...");
{% endhighlight %}

Si nunca has usado JavaScript, el código puede sacarte un poco de onda. Vamos a ver qué es lo que hace:

1. Utilizando la función `request` hace una solicitud de la página principal de Google
2. Imprimir "Waiting..."
3. Una vez que se obtiene la respuesta a la solicitud, llama la función `gotResponse`, que imprime el cuerpo del sitio web (después de imprimir "Waiting...")

La razón por la que esta sección de código *se ve rara* es que la función `gotResponse` **es un argumento** para la función `request`. El segundo argumento de la función `request` se llama `callback`, y es la función la función que debe ser utilizada cuando se reciba una respuesta! Con esto vemos los tres conceptos más importantes del modelo de programación de JavaScript:

* Funciones como objetos de primer nivel. La función `gotResponse` es **una variable** y un argumento para otra función.
* Programación asíncrona. El hilo principal **no espera** a que la página web sea obtenida inmediatamente, sino que continúa ejecutando el resto de la función princpal (e imprime "Waiting...")
* Programación orientada a eventos. Cuando el evento `"response"` ocurre, un mensaje es agregado a la fila de eventos - y una vez que el mensaje es leído por el hilo principal, la función `gotResponse` es ejecutada.

¿Difícil? Un poquito. ¿Importante? **mucho**.

## El hilo principal
Como lo comentamos, JavaScript funciona con un hilo. Esto simplifica mucho el esquema de programación. Dado que una función se ejecuta completamente de inicio a fin, el programador no necesita ocuparse de obtener locks, ni preguntarse qué puede ocurrir si su función es interrumpida.

Uno podría pensar: Pero no vale la pena crear un lenguaje simple de programar que es lento porque no es paralelo! Pues aquí está el secreto de JavaScript: aunque el código 'de usuario' se ejecuta en un hilo, las operaciones de Entrada-Salida se ejecutan en **otros hilos** manejados por el sistema operativo y el motor de JavaScript.

En pocas palabras **todo corre en paralelo con excepción de tu código**. Es decir, tu código corre en un solo hilo, pero la entrada-salida y las llamadas al sistema son **todas en paralelo**. Pero tú no te tienes que preocupar por ellas! Nada mal, ¿no?

## Node.js
En los últimos años, JavaScript se ha convertido en un lenguaje que también se utiliza para Backend! Esto gracias a Node.js, un entorno de programación que te permite correr programas de JavaScript fuera de un explorador de internet. Es muy utilizado en empresas grandes y chicas para crear servidores rápidos y sencillos.

Para darte una idea de cómo funciona JavaScript, puedes instalar [Node.js](https://nodejs.org), y correr el siguiente programa que hace un servidor web que muestra HelloWorld.

{% highlight javascript %}
var http = require("http");
var server = http.createServer(function(request, response) {
  response.writeHead(200, {"Content-Type": "text/html"});
  response.write("<!DOCTYPE "html">");
  response.write("<html>");
  response.write("<head>");
  response.write("<title>Hello World Page</title>");
  response.write("</head>");
  response.write("<body>");
  response.write("Hello World!");
  response.write("</body>");
  response.write("</html>");
  response.end();
});

server.listen(80);
console.log("Server is listening");
{% endhighlight %}

Y si gustas, ábrelo en tu explorador. De hecho, abre muchas páginas al mismo tiempo! Y ve cómo las atiende inmediatamente. Puedes crear un pequeño programa que haga conexiones en paralelo: Puede atender muchísimas - y en un solo hilo ; )

## Concluyendo
Pues vimos rápidamente algunos aspectos del modelo de programación de JavaScript. Es un lenguaje que se ha vuelto muy importante no sólo para programación web, sino también para programación de backend, y de visualización.

El entender el modelo de programación asíncrona es bueno para cualquier estudiante. Te recomiendo que intentes aprender un poco de JavaScript! Haz un proyecto, experimenta.

Si tienes alguna duda o curiosidad, puedes preguntar en los comentarios! ; )
