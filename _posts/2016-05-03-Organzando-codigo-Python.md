---
layout: post
title: Organizando tus módulos de Python - __init__.py
author: Pablo Estrada
author_site: http://iampablo.me
tags:
  - español
  - python
  - programming
  
---
Python es un lenguaje súper fácil de usar. Se ha vuelto muy popular en escuelas, investigación, y para "Data Science". Éste es un post sobre 'buenas prácticas' con tu códgo de Python.

## ¿Para qué es **__init__.py**?
El archivo \_\_init\_\_.py es utilizado para inicializar paquetes de Python. Imaginemos que tenemos un paquete de python llamado *package*, que además contiene un subpaquete llamado *subpackage*. Imaginemos que se ve así:

{% highlight python %}
package/
    __init__.py
    archivo1.py
    archivo2.py
    archivo3.py
    subpackage/
        __init__.py
        submodulo.py
{% endhighlight %}

En este caso, el archivo __init__.py le indica al intérprete de Python que el directorio *package* contiene un módulo, y que debe tratarlo como tal (es decir, hacer que sea posible importar los archivos como parte del módulo).

## ¿Qué pongo en el archivo **__init__.py**?
En general no es necesario poner nada en el archivo \_\_init\_\_.py, pero es muy común usarlo para realizar configuraciones e importar cualquier objeto necesario de nuestra librería.

Por ejemplo, en nuestro ejemplo, si el archivo `archivo1.py` contiene una clase llamada `Archivo`, podemos importarla con \_\_init\_\_.py para que esté disponible al *nivel de paquete*. Normalmente para importar esta clase, tendríamos que hacer lo siguiente:

{% highlight python %}
from package.archivo1 import Archivo
{% endhighlight %}

Pero podemos simplificarlo con nuestro \_\_init\_\_.py así:

{% highlight python %}
# En el archivo package/__init__.py
from archivo1 import Archivo

# En tu programa que utiliza el paquete package
from package import Archivo
{% endhighlight %}

Otra posibilidad bastanté útil de \_\_init\_\_.py es la variable \_\_all\_\_. Esta variable contiene la lista de módulos que serán importados al utilizar `import *`. Para nuestra estructura de ejemplo, si el \_\_init\_\_.py del directorio `subpackage/` contiene lo siguiente:

{% highlight python %}
__all__ = ['elmodulo1','elmodulo2']
{% endhighlight %}

Entonces al realizar lo siguiente:

{% highlight python %}
from subpackage import *
# Importa elmodulo1 y elmodulo2
{% endhighlight %}

Con estas herramientas puedes hacer que tus paquetes sean mucho más elegantes de importar y manejar. Ojalá haya resultado útil, y cualquier duda o comentario, adelante!

Posteriormente habrá otros posts sobre cómo usar Python mejor.

