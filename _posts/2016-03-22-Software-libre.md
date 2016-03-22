---
layout: post
title: ¿Cómo empezar a crear software libre?
author: Pablo Estrada
author_site: http://iampablo.me
tags:
  - español
  - software
  - opensource
  - programming
  
---
El software libre está presente en todas nuestras actividades tecnológicas. Desde [el sistema Android en nuestros celulares](https://github.com/android), [el sitema Linux](https://github.com/torvalds/linux) en la mayor parte de los servidores del mundo, y la librería [OpenSSL](https://github.com/openssl/openssl) que ayuda a encriptar tus comunicaciones. Es software muy importante, y grandes desarrolladores han empezado sus carreras con software libre (e.g. Miguel de Icaza y Linus Torvalds); sin embargo, también es un entorno desconocido y donde es difícil adentrarse.

Desafortunadamente, empezar a participar en proyectos de software libre no es sencillo. La curva de aprendizaje es larga y difícil; pero conforme el tiempo ha pasado, la comnidad del software libre se ha vuelto más accesible, y más *cool*.

¿Cómo que cool? Para empezar, [GitHub](https://github.com/) se ha convertido en la plataforma ideal y dominante para mantener proyectos de software libre. Cualquiera puede [unirse a GitHub](https://github.com/join?source=header-home) fácilmente y empezar a aprender cómo usarlo. A través de GitHub es posible acceder a la mayor parte del software libre! : )

Por otro lado, cada vez hay más empresas que abren sus proyectos al público. Por ejemplo está el software de *Machine Learning* [TensorFlow de Google](https://www.tensorflow.org/), o el lenguaje de programación [Swift, de Apple](https://swift.org/).

Por eso es que en este post vamos a tratar de  entender un poco la comunidad de software libre, y los distintos caminos para adentrarse en ella!

## Los distintos desarrolladores de software libre
No hay un solo perfil de desarrollador de software libre. Hay los que son desarrolladores de software libre profesionales, y hay quienes lo hacen como un hobby. También hay quienes hacen investigación relacionada con sus proyectos. Vamos a platicar rápidamente de quiéne son los desarrolladores que crean software libre hoy en día.

### *Kernel hackers* profesionales
Dado que el software libre es tan importante, para muchas empresas resulta útil contratar a desarrolladores cuya tarea es implementar los features que les interesan en software existente. Éste es el caso de [empresas como Oracle, Google, Intel, Facebook, IBM, Redhat, Canonical, etc.](http://bitybyte.github.io/assets/whowriteslinux2015.pdf) De hecho, hoy en día gran parte del kernel de Linux es desarrollado por miles de *kernel hackers* distribuídos en cientos de empresas.

En general, distintas compañías tienen distintos problemas. Por ejemplo, compañías como Facebook y Google buscan optimizar el núcleo de Linux para atender a millones de usuarios. Por otro lado, empresas como RedHat o Canonical trabajan en aplicaciones para que Linux sea utilizado por clientes de estas organizaciones. Y hay otras empresas que requieren desarrollar controladores para sus dispositivos, o portar software a sus arquitecturas. Hay pa' todos!

*Kernel hacking* es una de las áreas más difíciles y más retadoras de la programación. Si tu sueño es algún día ser un *Kernel hacker*, tienes que tener bases bien, *bien* sólidas en sistemas operativos y en general toda la pila de tecnologías que corren en tu computadora. Básicamente, échale muchas ganas, por que tienes muuucho que aprender ; )

### Desarrolladores de proyectos que son lanzados como software libre
También ocurre seguido que muchas empresas deciden lanzar sus productos como software libre. Un ejemplo simple es Facebook, que trabaja en [React.js](https://github.com/facebook/react), un framework para front end para desarrollo web, y [Apache Cassandra](http://cassandra.apache.org/), software para almacenamiento de *big data*, entre otros; o Google que mantiene el [lenguaje de programación Go](http://golang.org/), [Chromium](http://www.chromium.org/), la base del explorador Chrome; e infinidad de otros proyectos; e incluso Microsoft, que recientemente abrió el código de [.NET](https://github.com/Microsoft/dotnet) al mundo!

Los desarrolladores en estos proyectos típicalmente (pero no siempre) empiezan trabajando en un proyecto de código 'cerrado' dentro de su empresa, pero eventualmente su trabajo es lanzado al público. Cuando esto ocurre, la empresa sigue siendo el 'guía' del software, pero permite que otros se unan a contribuír. La ventaja de abrir su código es que crean una comunidad alrededor de su proyecto.

Si te interesa trabajar en un proyecto de este estilo, puedes ir a Github y buscar algún proyecto que te interese para empezar a participar! Por ejemplo, si contribuyes a React.js, algún día podrías ser considerado para ser contratado en Facebook o en Google (que desarrolla Angular.js).

### Estudiantes e investigadores
Otro perfil muy común de desarrollador de software libre es aquel de un investigador o estudiante de posgrado que trabaja en un laboratorio donde se desarrolla software libre. Un ejemplo es el [Laboratorio AMP](https://amplab.cs.berkeley.edu/) (Algorithms Machines People) de UC Berkeley, donde desarrollaron [Spark](http://spark.apache.org/research.html), software para análisis de *big data*; o el proyecto [DBPedia](http://wiki.dbpedia.org/), al que contribuyen [muchísimos laboratorios en distintas universidades de Europa](http://wiki.dbpedia.org/about/dbpedia-community).

La mayor parte de los estudiantes que forman parte de esta categoría son de posgrado, pero también es posible empezar a participar desde antes. Mi recomendación es que busquen realizar trabajos como su tésis y proyectos de clase en organizaciones de software libre (pero tampoco se sientan mal si no lo logran al principio. No es fácil).

Si sirve de inspiración, Linus Torvalds hizo pública su primera versión de Linux a los 21 años, cuando era estudiante de licenciatura en la Universidad de Helsinki : )

### Aficionados y amateurs
Otro grupo de desarrolladores de software libre es el de aficionados. Gente que contribuye en su tiempo libre. Estas personas usualmente son desarrolladores con un trabajo normal, pero que en su tiempo libre contribuyen al software libre. Es excelente para tu carrera, pero también es difícil tomar el tiempo extra que requiere trabajar en **otro** proyecto.

Si amas desarrollar software, y puedes hacerlo todo el día todos los días, probablemente puedas convertirte en un aficionado del software libre : )

## ¿Cómo empezar a contribuír?
Ya que hemos hablado de las distintas caras de los desarrolladores de software libre, ahora vamos a platicar de cómo puedes ir acercándote a la comunidad.

### Instala Linux (o utiliza un sistema Unix)
¿Qué? ¡Pero también hay software libre en Windows!... Sí, es cierto. Pero instalar Linux es el primer paso para ganar montones de experiencia como desarrollador, especialmente de software libre. Te permite exponerte a montones de software libre, y la mayor parte de la comunidad utiliza Linux.

Entiendo que es un poco intimidante, pero después de poco tiempo te acostumbrarás. Casi cualquier tipo de software puede ser substituído (¿Office? Usa Google Docs! ¿Visual Studio? Usa Eclipse,o aprende Vim ;).

Por otro lado, si tienes una Mac, no te preocupes. El sistema oeprativo de Mac es un tipo de Unix, y la mayoría del software disponible para Linux es portable a Mac fácilmente.

### Cuenta de GitHub
Primero que nada es bueno que abras una cuenta de GitHub. [Aprende a usar Git](http://rogerdudler.github.io/git-guide/index.es.html). Crea algunos repositorios con tus proyectos personales o de la escuela, y haz algunos commits. Invita a compañeros a trabajar concurrentemente en un repositorio.

Poco a poco irás aprendiendo cómo usar Git civilizadamente, y te familiarizarás con él. Git (y cualquier otra tecnología de control de versiones) es importantísimo en el desarrollo de software; y es una competencia muy importante para cualquier desarrollador. Te conviene aprenderlo aunque no te conviertas en un desarrollador de software libre.

### Escoge un proyecto
Hay varias formas de empezar a contribuir poco a poco. Lo mejor es familiarizarte íntimamente con software que utilizas normalmente. Por ejemplo, si usualmente programas en Python o JavaScript, puedes involucrarte en listas de correo para usuarios del lenguaje, y empezar a conocer librerías que estén siendo desarrolladas y novedades del lenguaje. Si encuentras una librería que te agrade, y con la que hayas trabajado un poco, y checa sus [*issues* en github](https://github.com/scikit-learn/scikit-learn/issues). Ahí puedes buscar alguno que parezca sencillo, y tratar de arreglarlo! : )

Conforme te familiarices con un proyecto como usuario, llegarás a entender en qué puede mejorar, y en dónde puedes ayudar a avanzarlo.

### Google Summer of Code
[El GSoC](http://summerofcode.withgoogle.com) es un evento que ocurre cada año. En este evento, estudiantes participan en organizaciones de software libre para desarrollar proyectos; y a cambio Google le paga un (jugoso) estipendio a los estudiantes.

Desafortunadamente, este año ya es muy tarde para participar en el Summer of Code, pero si eres estudiante, te recomiendo que mantengas los ojos abiertos. El GSoC es una oportunidad única para desarrollar un proyecto de software libre 'grande'; y se ve increíble en tu currículum ; )

### Conoce a otros con los mismos intereses
Participa en organizaciones de tu escuela y de profesionales. Aquí hay algunas organizaciones de software libre que pueden interesarte:

* [Grupo de Software Libre de la Facultad de Ingeniería, UNAM](https://github.com/unamfi) - La comunidad de software libre de la Facultad de Ingeniería de la UNAM
* [Festival Latinoamericano de Instalación de Software Libre](http://www.flisol.info/) - Un festival organizado cada año para ayudar a interesados a instalar Linux y otros tipos de software libre!
* Grupos en tu Universidad o Empresa
* (Otros grupos por favor contáctenme para que los agregue a la lista!)

### Construye proyectos con Software Libre
La participación en software libre no es sólo contribuyendo código. Cuando estés creando un proyecto o un sistema, recuerda que hay software libre para todo. La experiencia de usuarios es importantísima para los desarrolladores - y conocer software como usuario es un primer paso para contribuir.

También es importante recordar que al construir proyectos y sistemas con software libre, tú eres libre de avanzar tu proyecto y el software del que depende en la dirección que te interese.

## Para terminar
El camino del desarrollador es largo (e interesante). La idea es que siempre sigan aprendiendo, y mejorando. Apoyar proyectos de software libre vale mucho la pena. Algunas ventajas de tener experiencia como desarrollador de software libre son:

* Familiarizarse con código de alta calidad
* Crear un registro público de tu habilidad como programador
* Aprender a trabajar con otras personas en proyectos reales

Todos estos puntos te convierten en un mejo desarollador, y *especialmente* en uno más valioso y mucho más contratable ; ).

Por todo esto, es recomendable mantenerse activo como desarrollador y si el tiempo y la determinación te lo permiten, participar en la comunidad de software libre! Si tienes cualquier duda o interés, puedes preguntarme [a mí directamente](http://iampablo.me/), o buscar un mentor experimentado que pueda ayudar a guiarte!

#### Una nota más: Una falsa dualidad
Otra cosa que es importate es **abandonar la dualidad del software libre y el software "privativo"**. No son enemigos, y no son contrarios. Empresas pueden decidir mantener su software privado por razones propias - y pueden decidir abrir su código por otras razones. Empresas como Google, Microsoft, Apple, entre otras, han contribuído a la ciencia de la computación con software libre y con software privado, y debemos aprender a apreciar ambas!

Comentarios y dudas son bienvenidos! Hasta la próxima!
