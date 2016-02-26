---
layout: post
title: Las Tortillas nos Unen
author: Pablo Estrada
author_site: http://iampablo.me
language: spanish
tags:
  - español
  - data analysis
  - mexico
  - 
---
Hace poco descargué una base de datos con información de negocios de "manufactura" [del sitio web del INEGI](http://www3.inegi.org.mx/sistemas/descarga/).  La información está disponible para todos, y hay
muchas cosas interesantes. Me puse a jugar con esos datos, y decidí hacer mi primera visualización de mapa con
la información de los negocios y algunos datos extra.

Al estar observando los datos, me di cuenta de que el tipo de negocio más común en la base de datos es el de 
tortillerias. Así es: tenemos más tortillerías en México, que cualquier otro tipo de negocio que fabrique algo. 
Se me hizo muy chistoso. Decidí estudiarlo en un pequeño mapa del Distrito Federal; y para no mostrar solamente 
tortillerías, busqué información sobre la población y el producto interno bruto de cada delegación. Todos
estos datos los pueden visualizar en el mapa que ven a continuación:

<iframe id="map-1113" src="http://pabloem.github.io/inegi/df_d3_es.html" width="950" height="700" frameborder="0" scrolling="no"></iframe>

Hay muchos patrones interesantes. Por ejemplo, el número de tortillerías y la población tienen casi los mismos
colores en el mapa!  esto significa que no importa que seamos ricos o pobres: Todos los Defeños comemos bastantes tortillas. Sin importar el PIB, ni el PIB per cápita; la concentración de tortillerías en el Distrito Federal 
es más o menos uniforme, de entre 3 y 10 por cada 1000 personas. 

Por otro lado, se pueden ver unos datos que no son tan alentadores: Las disparidades en el Producto Interno Bruto;
y especialmente las del PIB per cápita. Podemos ver que la delegación más poblada (Iztapalapa) es también 
una de las delegaciones con el *menor* PIB del DF; mientras que la Miguel Hidalgo tiene un PIB casi 7 veces
más grande. Esto habla de muchísima desigualdad. Claro, el PIB no representa exactamente el nivel de ingresos 
de los habitantes de cada zona; sino que representa la riqueza generada en esas zonas cada año - y claro,
zonas con negocios como las delegaciones Cuauhtémoc y Miguel Hidalgo tienen un PIB alto, aunque muchos de 
los trabajadores vengan desde otras zonas. 

Sin embargo, las disparidades no dejan de ser nada menos que *escandalosas*. De hecho, el PIB per cápita de la 
delegación Miguel Hidalgo es comparable con el de los países más ricos del mundo (hagan la conversión: son unos 
80,000 USD. Equivalente al de Suiza!); mientras que el PIB per cápita de la delegación Iztapalapa es de unos 
2,400 USD: Comparable al de Gana; y apenas 3% del de la Miguel Hidalgo.

De nuevo, no es posible atribuir estas diferencias solamente a la desigualdad económica - y tampoco es posible
decir que es todo debido a la distribución desigual de empresas y medios para generar riqueza. También hay que
considerar que una ciudad está muy interconectada, y que para comprenderla, un pequeño mapa con estadísticas
no es suficiente.

De cualquier manera, podemos recordar que aunque la ciudad está llena de contrastes, hay algo donde todos estamos
en la misma página: amamos nuestra comida, y cómo no? Nuestra comida es la mejor del mundo. Y no importa si vives
en Milpa Alta, Iztapalapa, la Cuauhtémoc o la Miguel Hidalgo: las tortillas nos unen.

<script src="http://pabloem.github.io/images/map1113.js">
</script>
