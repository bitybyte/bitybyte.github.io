--- 
layout: post 
title: Las 3 Lecciones que aprendí tras una semana en Google
author: Pablo Estrada
author_site: http://iampablo.me 
tags:
  - español
  - cs
  - industry
  
--- 
En Marzo de 2016 recibí una de las noticias más importantes de mi vida: Una
oferta de trabajo de Google. Dos semanas después acepté la oferta, y para
Septiembre del mismo año me uní a la empresa. Ésta es la historia de mi primera
demana en el gigante de Mountain View.

La oferta era para la posición de desarrollador de software en la oficina de 
Seattle, en Estados Unidos. Específicamente, el equipo que me contrató es el que
está a cargo de [Apache Beam / Cloud Dataflow](https://cloud.google.com/dataflow/).
Un producto de análisis de datos distribuído y a gran escala.

El 6 de Septiembre de 2016 fue mi primer día en la empresa. Toda la expectativa 
que había estado juntando por meses se acercaba a una conclusión. Mi primera 
semana sería de orientación para nuevos empleados, y la orientación se hace en 
las oficinas matriz en Mountain View, así que fui enviado allá para mi 
orientación.

Lo que siguió fue un bombardeo de información sobre la misión y los principios
de la compañía, las prácticas de desarrollo de software, y la infraestructura
técnica de la empresa.

## Primera semana
 
### Los primeros dos días
En el primer día llegué a las oficinas a las 8 de la mañana y empezó la fiesta: 
Tras presentar mi pasaporte, recibí mi tarjeta electrónica para poder entrar a 
cualquier edificio. Después de eso comimos el desayuno -chilaquiles!- y recibimos 
nuestras computadoras y algunos regalitos.

![Think Big]( {{site.url}}/images/think_big_noogler.jpg )

El primer día estuvo lleno de presentaciones no-técnicas, donde discutimos la 
misión de Google (“Organizar la información del mundo y permitir que sea 
universalmente accesible y útil”), beneficios de empleados (seguro médico, apoyo
al crecimiento profesional, vacaciones), y otras cosas sencillas. Fue una sesión
tranquila.

Al final del día conocí a la mitad de mi equipo que está en Mountain View,
California. Son unas 30 personas, y todos son muy amables y divertidos. También 
son excelentes desarrolladores. Recibí una clase personal sobre cómo funciona el
proyecto y la infraestructura del proyecto (también conocida como 
infrastructorchor). Aquí fue donde aprendí mi primera lección importante:

**Lección 1: Sí: es intimidante. Y sí, es normal. Al unirte a una compañía, es
importante buscar una donde la mayoría sea tanto o más inteligente que tú: Es la
mejor manera de crecer y aprender. Te sentirás intimidado por la cantidad de
información, y por los demás. Pero es normal: Es lo mejor para tí.**

El segundo día de orientación de nuevo consistió de clases y conferencias que no
eran técnicas o de programación. La parte más interesante fue un estudio de una 
técnica de diseño llamada [Design Thinking](https://en.wikipedia.org/wiki/Design_thinking),
donde se busca diseñar un producto considerando especialmente las necesidades 
del usuario, en vez de la capacidad tecnológica o la opinión de desarrolladores
y diseñadores.

![Orientation Room]( {{site.url}}/images/orientation_room.jpg )

### Los últimos días - software, hardware, y todo.

Los últimos días de mi primera semana fue donde todo se puso más interesante.
Finalmente llegó la hora de aprender cómo es la vida de un desarrollador en 
Google, qué clase de herramientas hay que usar, y cómo usarlas:

- La primera clase estudiamos como se evalúa a un desarrollador, y la relación
  que va a tener con su manager, su mentor, y su equipo de desarrollo.
- La segunda clase estudiamos el developer workflow. Es decir, las herramientas
  que usa Google para control de versiones, compilar, testear y en general
  desarrollar nuevo código.
- La tercera clase estudiamos la estructura de los data centers de Google. Fue
  una clase muy muy interesante - y muy muy confidencial. Basta decir que la
  infraestructura de Google es IN-CRE-I-BLE. Si les da curiosidad, pueden checar
  [este artículo](https://research.googleblog.com/2015/08/pulling-back-curtain-on-googles-network.html),
  o [este otro](http://highscalability.com/google-architecture)!
- La última clase técnica fue sobre la estructura de los servicios que existen
  dentro de Google. También una plática confidencial, pero existen artículos
  públicos que describen
  [el sistema de archivos utilizado en Google](http://static.googleusercontent.com/media/research.google.com/en//archive/gfs-sosp2003.pdf),
  sus [herramientas](http://static.googleusercontent.com/media/research.google.com/en//archive/mapreduce-osdi04.pdf)
  de [procesamiento de datos](http://pages.cs.wisc.edu/~akella/CS838/F12/838-CloudPapers/FlumeJava.pdf), 
  e incluso  sus
  [bases de datos transaccionales](http://static.googleusercontent.com/media/research.google.com/en//archive/bigtable-osdi06.pdf) que
  son muy interesantes, y que vale la pena que cualquier estudiante cheque!

Con todas estas clases y prácticas técnicas, fue difícil retener todos sus 
contenidos. Aquí apareció mi segunda lección importante:

**Lección número 2: No tengas miedo de hacer preguntas. Cuando te unes a un
equipo de desarrollo de software, es importante que aprendas lo más rápido
posible, para ganarte la confianza de tu equipo. Para aprender tienes que
quitarte el miedo a preguntar sobre lo que no sabes.**

La mejor parte de la semana fue el evento llamado TGIF. 
En este evento, los líderes de la empresa (e.g. [Sergey](https://en.wikipedia.org/wiki/Sergey_Brin)
y [Larry](https://en.wikipedia.org/wiki/Larry_Page))
hablan frente a una audiencia de empleados de Google, y comentan sobre los
nuevos proyectos de la empresa, además de hacer un montón de chistes tontos y
darle la bienvenida a los nuevos empleados. Además hay un montón de
cerveza gratis. Tuve la suerte de sentarme súper cerca del
escenario!

![TGIF at Google]( {{site.url}}/images/google_tgif.jpg )

Un TGIF se ve más o menos así - aunque no siempre hay referencias de Star Wars.
TGIF me enseñó mi última lección importante de la semana:

**Lección 3: No pierdas de vista tu entorno. Como desarrolladores, es fácil
enfocarnos completamente en nuestro código; y las tareas diarias. Es importante
recordar que la comunicación y el intercambio de ideas son semillas de inovación,
y muchos proyectos de Google surgieron por iniciativa de los mismos ingenieros:
Gmail, MapReduce, etc.**

El Fin.  A las 4:30pm del Viernes terminaron todas las presentaciones y
clases. A esa hora tuve que correr al aeropuerto más cercano, porque mi vuelo de
regreso a Seattle salía a las 6:50. Me fui muy contento, y con muchas ganas de
conocer a mi equipo en mi propia oficina - muy enriquecido, y muy emocionado por
las nuevas experiencias que vendrán.
