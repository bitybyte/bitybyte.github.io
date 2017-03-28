---
layout: post
title: Los passwords son mejores con Sal y Pimienta
author: Pablo Estrada
author_site: http://iampablo.me
tags:
  - español
  - cs
  - programming
  - security

---

¿Alguna vez has desarrollado un sistema web? Por ejemplo, una página con PHP-MySQL,
Django-Postgress, o Node-MongoDB? ¿Tu sistema tiene cuentas de usuario con passwords
guardados en la base de datos? Y... le pusiste sal a tus passwords?

El desarrollo de software no es sencillo. Siempre hay más trabajo que tiempo, y
la fase de diseño puede ser infinita. A todos nos gusta **saltar directo al
desarrollo**; sin embargo, hay algunas cosas que necesitan **muuucho cuidado** 
cuando desarrollamos sistemas de software. Una de ellas es 
**cómo tratar con passwords en tu sistema**. Parecería sencillo, ¿no? 
En tu base de datos creas una tabla `USERS`, y ahí guardas el nombre de usuario 
(`username`), y el password (`password`). Algo así:

{% highlight python %}
| username | password    |
|----------|-------------|
| pepito   | password123 |
{% endhighlight %}

Desafortunadamente, esto **es una idea horrorosa**. Aunque nos gustaría pensar que 
somos muy listos, la seguridad de cómputo es un tema
muy difícil: Cuando diseñas un sistema tienes que considerar la posibilidad de que
**un hacker se robe tu base de usuarios**. Si esto llegara a pasar (y de hecho
[ocurre](https://en.wikipedia.org/wiki/Ashley_Madison_data_breach) con 
[mucha](https://en.wikipedia.org/wiki/Yahoo!_data_breaches) frecuencia), 
imagina el efecto a **tu reputación**, y a todas las cuentas electrónicas de
tus usuarios cuando sus passwords sean expuestos!

Por eso, en este post te vamos a dar **tres tips** para el manejo de passwords en tu
sistema.

## Primero hay que hashear
La primera lección es que nunca, **nunca** debes guardar los passwords de tus
usuarios en tu base de datos. Lo que debes hacer es **hashear el password**.

### ¿Qué es *hashear*?
Una función de hash te permite cifrar, o encriptar un password de manera que
no sea posible recuperar el password a partir del hash. Por ejemplo, dado un
usuario cuyo password es `password123`, podemos utilizar el módulo `sha` de 
Python para proteger el password:

{% highlight python %}
from sha import sha
password = 'password123'
password_hasheado = sha(password).hexdigest()
print password_hasheado
# 'cbfdac6008f9cab4083784cbd1874f76618d2a97'
{% endhighlight %}

Es decir que al guardar cuentas de usuario, **nunca guardes el password**. Debes
guardar **el password hasheado**. Así, si tu base de datos es robada, los hackers
no van a poder leer los passwords de tus usuarios.

Tu tabla de base de datos se ve así:

{% highlight python %}
| username | hash                                     |
|----------|------------------------------------------|
| pepito   | cbfdac6008f9cab4083784cbd1874f76618d2a97 |
{% endhighlight %}

## Segundo: Échale sal
Hashear el password de tus usuarios es una muy buena idea, pero cuenta con una
vulnerabilidad muy interesante: Resulta que la mayoría de la gente tiene 
passwords **muy malos**. Por ejemplo, muchos usuarios tienen passwords 
como `12345678`, `password`, `qwerty`, `google`, etc. ¿Sabes qué significa?

Esto quiere decir que es muy fácil realizar un **ataque de diccionario** a tu
base de datos. Un diccionario es una lista muy grande de passwords comunes y 
de sus hashes, y son muy fáciles de conseguir 
([aquí](https://crackstation.net/buy-crackstation-wordlist-password-cracking-dictionary.htm), 
[aquí](https://wiki.skullsecurity.org/Passwords), o
[aquí](https://github.com/danielmiessler/SecLists)). Con un ataque de este tipo
es posible encontrar los passwords de la mayoría de tus usuarios.

Aquí es donde podemos utilizar un truco interesante y muy sencillo: Generar una
cadena de caracteres aleatoria para cada password (de preferencia que sea única,
y que no sea corta), guardarla en la base de datos, y **pegársela al password** 
a la hora de hashearlo. A esta cadena aleatoria se le llama **la sal**.

{% highlight python %}
# Generamos una cadena de 6 caracters
sal = random_string(6)
print sal
# '8nebce'
password = 'password123'
password_con_sal_hasheados = sha(password+sal).hexdigest()
# '26cf631a9488ed9b2135e7c43e4a4912a57f16bd'
{% endhighlight %}

¿Ves cómo al utilizar **la sal**, el hash es **completamente distinto**? Esto quiere
decir que el hacker **no puede usar un diccionario** para encontrar los passwords
de tus usuarios. El hacker va a tener que crear **un diccionario para cada sal 
distinta**. Esto aumenta **significativamente** la cantidad de trabajo (y de recursos
de cómputo) que un hacker necesita para atacar tu base de datos. 

Tu tabla de usuarios ahora se ve así:

{% highlight python %}
| username | hash                                     | salt   |
|----------|------------------------------------------|--------|
| pepito   | cbfdac6008f9cab4083784cbd1874f76618d2a97 | 8nebce |
{% endhighlight %}

Cuando quieres validar el nombre del usuario, sólo tienes que concatenar el password
insertado por el usuario, la sal que tienes en la base de datos y comparar el hash
resultante con el hash guardado en la base de datos. Simple, y seguro!

## Tercero: Échale pimienta
Hay un truco extra que puedes usar para proteger los datos en tu base. Puedes
ponerle pimienta a tus passwords.

Una de las características principales de una función de hash es que es bastante
rápida de calcular. Por esto es que los ataques de diccionario son factibles. Sin
embargo, un ataque a una base de datos con passwords hasheados requiere
que un hacker calcule una función de hash **miles de millones de veces**.

Agregarle pimienta a los passwords también consiste en utilizar una cadena
de caracteres aleatoria - esta vez más simple, y más corta. Por ejemplo, de
un caracter:

{% highlight python %}
# Generamos una cadena de 6 caracters
sal = random_string(6)
pimienta = random_string(1)
print pimienta
# 'q'
password = 'password123'
password_sal_pimienta_hash = sha(password+sal+pimienta).hexdigest()
# 'e6759e00018ad2aee252a9a9999ac79a557b3730'
{% endhighlight %}

La pimienta nos permite, una vez más, modificar completamente el hash. La diferencia
es que la pimienta **no se guarda en la base de datos**, ni en ningún lado. Cuando
validas el password de un usuario, debes intentar **todas las posibilidades** de
pimienta (que, como la pimienta es un byte, son 256).

Es decir que la función de validación es la siguiente:

{% highlight python %}
def validar_usuario(username, password):
    hash, sal = consult_database(username)
    for i in range(256):
        pimienta = chr(i)
        if sha(password+sal+pimienta).hexdigest() == hash:
            return True
    return False
{% endhighlight %}

## El pilón: Utiliza funciones de hash robustas
En el mundo existen decenas de funciones de hash que puedes utilizar en tu sistema.
Cuando desarrolles tu sistema, tómate 10 minutos para buscar una función de hash
que no sea fácil de violar.

Por ejemplo, la función MD5 (disponible en todos los lenguajes), es muy fácil de 
romper. Esto quiere decir que es muy fácil encontrar un password que le corresponda 
a cierto valor de hash.

Cuando el momento llegue, te recomiendo que revises 
[uno de tantos manuales disponibles](http://security.blogoverflow.com/2013/09/about-secure-password-hashing/)
en línea [para un sistema de usuarios seguro](https://crackstation.net/hashing-security.htm).

## ¿Usuario? Usa passwords difíciles
Si tenías cuenta en Yahoo, tu password ya fue robado. Y no es la última vez que
algo así ocurre: Asume que una versión hasheada de todos tus passwords puede ser
robada fácilmente. Con esto en mente, haz un **buen esfuerzo** para que tu password
sea [fácil de recordar, pero **difícil** para que una computadora lo adivine](https://xkcd.com/936/).

Saludos!
