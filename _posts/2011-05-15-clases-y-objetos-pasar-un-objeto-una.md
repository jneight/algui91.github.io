---
id: 189
title: 'Clases y Objetos &#8211; Pasar un objeto a una función'
author: Alejandro Alcalde
layout: post
guid: http://elbauldelprogramador.org/clases-y-objetos-pasar-un-objeto-a-una-funcion/
permalink: /clases-y-objetos-pasar-un-objeto-una/
blogger_blog:
  - www.elbauldelprogramador.org
  - www.elbauldelprogramador.org
  - www.elbauldelprogramador.org
blogger_author:
  - Alejandro Alcaldehttps://profiles.google.com/117030001562039350135noreply@blogger.com
  - Alejandro Alcaldehttps://profiles.google.com/117030001562039350135noreply@blogger.com
  - Alejandro Alcaldehttps://profiles.google.com/117030001562039350135noreply@blogger.com
blogger_permalink:
  - /2011/05/clases-y-objetos-pasar-un-objeto-una.html
  - /2011/05/clases-y-objetos-pasar-un-objeto-una.html
  - /2011/05/clases-y-objetos-pasar-un-objeto-una.html
categories:
  - C
---
<div class="icocpp">
</div>

Para usar una función son necesarios tres pasos:

  * Declaración de la función.
  * Definición de la función, de la tarea a realizar.
  * Llamada de la función y realización de la tarea.

Vamos a estudiar cómo se pasa una variable a una función en uno de sus  
argumentos. 

  
<!--more-->

### Paso por valor

{% highlight cpp %}>int funcion (int parm); // declaración
funcion (arg);          // llamada
inf funcion (int parm)  // definición
{
  cout &lt; &lt; parm &lt;&lt; 'n';
  //...
  parm=88;
}
{% endhighlight %}

Así, cuando la variable arg se pasa por valor a una función, la función recibe una  
copia de arg. Cualquier cambio en la copia parm dentro del cuerpo de la función no  
afecta para nada al valor de arg. La función no puede, por tanto, alterar directamente el  
parámetro pasado por valor. Así pues, durante el curso de la llamada a la función  
existen la variable arg y su copia parm. Cuando se sale de la función, la variable parm  
está fuera de ámbito y se libera la memoria que ocupaba. En este caso, se asigna el valor  
88 a parm, no a arg. 

### Paso por dirección

{% highlight cpp %}>int funcion(int* parml); // declaración
funcion (&#038;arg1);         // llamada
int funcion (int * parm) // definición
{
  cout &lt; &lt; *parm &lt;&lt; 'n';
  //...
  *parm=88;
}
{% endhighlight %}

Se pasa a la función no arg, sino &arg, la dirección de arg. La dirección de arg,  
&arg, se pasa por valor, y la función recibe una copia de ésta en parm. La función  
accede, por tanto, a la dirección de arg a través de la copia que recibe, y de este modo  
puede o no modificar el valor de arg. En este caso, se asigna el valor 88 a la dirección  
apuntada por parm, 88 es por tanto asignado a arg. 

## El Concepto de referencia  


Las referencias son una evolución de los punteros, y son exclusivas del lenguaje  
C++, Facilitan mucho el trabajo de la programación sin los inconvenientes que presenta  
la notación de punteros. Pasar una variable a una función por referencia es mucho más  
claro que hacerlo por dirección. Se puede entender la referencia como un alias o apodo  
de una variable en el ámbito de una función. 

Declaración de una referencia: ínt& ir=i; 

ualquier cambio que experimente ir, lo experimenta i, y viceversa. La  
potencialidad de las referencias está en el paso de una variable a una función, y en  
segundo lugar -aunque no lo veremos- en la posibilidad de, que las funciones devuelvan  
referencias. 

### Paso por referencia  


{% highlight cpp %}>int funcion (int&#038; parm);  // declaración
funcion (arg);            // llamada
int funcion (int&#038; parm)   // definición
{
  cout &lt; &lt; parm &lt;&lt; 'n';
  parm=88;
}
{% endhighlight %}

asigna el valor 88 a parm, pero parm, es el alias de arg, por tanto, modificando parm se  
modifica arg. Como podernos apreciar, el paso por referencia es semejante al paso por  
valor, excepto en la declaración de la función.

<div class="sharedaddy">
  <div class="sd-content">
    <ul>
      <li>
        <a class="hastip" rel="nofollow" href="http://twitter.com/home?status=Clases y Objetos &#8211; Pasar un objeto a una función+http://elbauldelprogramador.com/clases-y-objetos-pasar-un-objeto-una/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Twitter" target="_blank"><span class="iconbox-title"><i class="icon-twitter icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="http://www.facebook.com/sharer.php?u=http://elbauldelprogramador.com/clases-y-objetos-pasar-un-objeto-una/&t=Clases y Objetos &#8211; Pasar un objeto a una función+http://elbauldelprogramador.com/clases-y-objetos-pasar-un-objeto-una/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Facebook" target="_blank"><span class="iconbox-title"><i class="icon-facebook icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="https://plus.google.com/share?url=Clases y Objetos &#8211; Pasar un objeto a una función+http://elbauldelprogramador.com/clases-y-objetos-pasar-un-objeto-una/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en G+" target="_blank"><span class="iconbox-title"><i class="icon-google-plus icon-2x"></i></span></a>
      </li>
    </ul>
  </div>
</div>

<span id="socialbottom" class="highlight style-2">

<p>
  <strong>¿Eres curioso? » <a onclick="javascript:_gaq.push(['_trackEvent','random','click-random']);" href="/index.php?random=1">sigue este enlace</a></strong>
</p>

<h6>
  Únete a la comunidad
</h6>

<div class="iconsc hastip" title="2240 seguidores">
  <a href="http://twitter.com/elbaulp" target="_blank"><i class="icon-twitter"></i></a>
</div>

<div class="iconsc hastip" title="2452 fans">
  <a href="http://facebook.com/elbauldelprogramador" target="_blank"><i class="icon-facebook"></i></a>
</div>

<div class="iconsc hastip" title="0 +1s">
  <a href="http://plus.google.com/+Elbauldelprogramador" target="_blank"><i class="icon-google-plus"></i></a>
</div>

<div class="iconsc hastip" title="Repositorios">
  <a href="http://github.com/algui91" target="_blank"><i class="icon-github"></i></a>
</div>

<div class="iconsc hastip" title="Feed RSS">
  <a href="http://elbauldelprogramador.com/feed" target="_blank"><i class="icon-rss"></i></a>
</div></span>