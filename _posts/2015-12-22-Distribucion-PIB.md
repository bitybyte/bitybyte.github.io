---
layout: post
title: Población y producción en el territorio Mexicano
author: Pablo Estrada
author_site: http://iampablo.me
language: spanish
tags:
  - español
  - mexico
  - data analysis
 
---
Hace unas semanas encontré un mapa en TopoJSON de México, y decidí utilizar información que obtuve del [sitio web del INEGI](http://www.inegi.org.mx) para hacer una pequeña visualización de Producción bruta total, y población en el territorio Mexicano. Aquí la pueden ver:

(Nota: Las barras de abajo representan población y producción bruta por municipio. Están coloreadas por quintiles sobre la producción. Es decir que la zona morada corresponde a los municipios que producen 20% de la riqueza, y donde vive 2.36% de la población. Es posible interactura con la visualización haciendo click en las barras del fondo.)

<iframe src="http://pabloem.github.io/inegi/gdp/index.html?lang=es" width="760" height="720" frameborder="0" scrolling="no"> </iframe>

La visualización me gustó bastante, porque es posible ver varios patrones interesantes. Primero que nada, el hecho de que la economía Mexicana se ha diversificado, y ahora es muy variada. El petróleo aún es muy importante, y la región cerca del [Complejo Cantarell](https://es.wikipedia.org/wiki/Complejo_Cantarell) contiene a algunos de los municipios más productivos del país (e.g. Carmen, Paraíso); sin embargo, los 10 municipios más productivos del país están bastante repartidos geográficamente (en Cd. de México, Monterrey, Guadalajara, Toluca y la región de Cantarell). Conforme seleccionamos más municipios, más y más áreas del país se colorean, desde centros turísticos, ciudades fronterizas, centros industriales y grandes zonas metropolitanas.

Desafortunadamente, hay un par de cuestiones menos agradables que es posible observar. Por un lado está la desigualdad económica: 90% de la producción es realizada en 140 municipios, donde solamente vive 50% de la población. Esto significa que el otro 50% vive lejos de las principales zonas productivas (aunque en lugares como la ciudad de México, esto no significa que el resto de la población no pueda recibir los beneficios de la riqueza producida en municipios aledaños).

El otro hecho que es posible observar, es la pobreza al sur del país, partícularmente en el estado de Oaxaca, donde la mayoría de los municipios permanecen obscuros hasta cerca del final. Claro, esto es en parte debido a que Oaxaca tiene muchísimos municipios (60 de los cuales se llaman San Juan!:)), y la producción económica está dividida en más piezas - pero el hecho de la pobreza en el sur del país persiste.

En fin, espero que les haya agradado. Todos los comentarios son bienvenidos, y el [repositorio en Github](https://github.com/pabloem/inegi/) también está disponible. Hasta pronto! ; )
