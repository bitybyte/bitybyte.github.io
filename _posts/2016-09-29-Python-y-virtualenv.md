--- 
layout: post 
title: Python en entornos aislados - virtualenv
author: Pablo Estrada
author_site: http://iampablo.me 
tags:
  - español
  - python
  - industry
  
--- 
Este post va a ser bastante sencillo. Les voy a contar de una herramienta
de desarrollo en Python llamada `virtualenv`; que te permite crear entornos
*aislados* para probar y desarrollar proyectos en Python. La ventaja de 
`virtualenv` es que te permite tener varias instalaciones funcionales de
Python en una misma máquina.

¿Y qué? Pues eso te permite trabajar con diferentes versiones de librerías
y paquetes (por ejemplo, versiones de desarrollo posteriores a los paquetes
públicos en PyPi).

## Instalación
Para instalar `virtualenv`, necesitas el manejador de paquetes de 
Python: `pip`. Si no tienes `pip`, [instálalo de una vez](https://pip.pypa.io/en/stable/installing/), 
es súper útil. 

Una vez que tengas `pip`, puedes instalar `virtualenv` así:

{% highlight shell %}
$> [sudo] pip install virtualenv
{% endhighlight %}

## Configuración y uso
Ya con `virtualenv` instalado, es muy fácil configurar entornos aislados para
tu desarrollo. Por ejemplo, digamos que quieres desarrollar tu propio proyecto
en el directorio `proyectos/foobar/`. Puedes comprobar que tu ejecutable de python
cambia así:

{% highlight shell %}
$> which python
# Imprime: /usr/bin/python
$> cd proyectos/foobar/
$> virtualenv venv # Esto crea el directorio venv/
$> source venv/bin/activate # Esto activa el nuevo entorno
$> which python
# Imprime: /home/pablo/proyectos/foobar/venv/bin/python
$> which pip
# Imprime: /home/pablo/proyectos/foobar/venv/bin/pip
{% endhighlight %}

Con esto puedes tener tu propio ambiente de Python, y cualquier paquete que
instales o quites no afectará tu sistema.

**Recuerda** que esto sólo afecta la terminal donde estás trabajando. Necesitas 
activar tu `virtualenv` en todas las terminales en que quieras trabajar!

## Terminaste? Desactiva tu virtualenv
Si quieres volver a utilizar tu instalación normal de python, puedes cerrar tu terminal
y abrir una nueva, pero si no quieres, simplemente usa el comando `$> deactivate`

## Otras ventajas
`virtualenv` tiene muchísimas ventajas. Algunas son:

* No importa que no seas `root` en tu sistema, puedes instalar lo que quieras.
* Instalaste algo que no debías? No hay problema!
* Quieres una versión especial de una librería? Adelante!
* Te molesta que librerías actualizadas afecten tus aplicaciones? 
Crea un entorno aislado para la aplicación.

# Fin.
Por todo esto, les recomiendo que utilicen `virtualenv` para todos sus proyectos
de desarrollo de Python. Cualquier pregunta, por Facebook o en los comentarios!
