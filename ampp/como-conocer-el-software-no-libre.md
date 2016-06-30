---
title: 'Cómo conocer el software &#8220;no libre&#8221; instalado en nuestro equipo'

layout: post.amp

categories:
  - aplicaciones
  - curiosidades
  - How To
  - linux
main-class: "linux"
color: "#2196F3"
---
<div class="icoso">
</div>

Este programa lo ví en [ProyectosBeta][1].
Seguro que en nuestro equipo tenemos montones de aplicaciones instaladas, de las cuales muchas serán **no libres**, con el programita **vrms** podemos conocerlos de forma sencilla.

Los pasos a seguir son los siguientes:


<!--ad-->

Instalamos el programa:

```bash
sudo aptitude install vrms
```

Y lo ejecutamos con `vrms`

El resultado es el siguiente:

<div class="separator" style="clear: both; text-align: center;">
<a href="https://4.bp.blogspot.com/-wWUOaA33nCk/TdN2JjQ8OxI/AAAAAAAAAgM/nxfKbEuZCnE/s1600/vrms.png" imageanchor="1" style="margin-left:1em; margin-right:1em"><amp-img layout="responsive" border="0" height="225" width="400" src="https://4.bp.blogspot.com/-wWUOaA33nCk/TdN2JjQ8OxI/AAAAAAAAAgM/nxfKbEuZCnE/s400/vrms.png" /></a>
</div>

Para que nos salga la el dibujito de **Stallman** hay que seguir los siguientes pasos.

```bash
$ sudo aptitude install vrms cowsay
$ sudo mv rms.cow /usr/share/cowsay/cows/rms.cow
$ cowsay -f rms -W 60 `vrms`

```

<div class="separator" style="clear: both; text-align: center;">
<a href="https://3.bp.blogspot.com/-Hur9i5TORyM/TdN5Q19CliI/AAAAAAAAAgU/rhmM1JOnJao/s1600/stallman.png" imageanchor="1" style="margin-left:1em; margin-right:1em"><amp-img layout="responsive" border="0" height="256" width="238" src="https://3.bp.blogspot.com/-Hur9i5TORyM/TdN5Q19CliI/AAAAAAAAAgU/rhmM1JOnJao/s400/stallman.png" /></a>
</div>

* * *Fuente:

[Proyectosbeta][2]



 [1]: http://proyectosbeta.blogspot.com
 [2]: http://proyectosbeta.blogspot.com/2011/05/crear-la-cara-de-richard-stallmann-con.html

{% include toc.html %}