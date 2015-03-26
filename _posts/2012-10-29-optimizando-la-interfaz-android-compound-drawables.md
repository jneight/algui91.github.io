---
id: 1006
title: 'Optimizando la interfaz Android &#8211; Compound Drawables'
author: Alejandro Alcalde
layout: post
guid: /?p=1006
permalink: /optimizando-la-interfaz-android-compound-drawables/
categories:
  - android
tags:
  - android listview con imagenes
  - android listview example
  - compound drawables
  - curso android pdf
  - lint warning
  - optimizacion de listas android
  - optimizar layout
---
Hace poco, mientras escribía un [CustomAdapter][1] para una aplicación en la que estoy trabajando, descubrí una nueva característica gracias a la herramienta Lint del sdk, los compound drawables.

#### Compound Drawables

Consiste en simplificar un [layout][2] cuando éste conste de un ImageView y un TextView. Suele ser frecuente encontrarse en una lista de elementos una imagen junto a un texto. Algo así:  
  
<!--more-->

  
&nbsp;

{% highlight xml %}>&lt;LinearLayout
    <!--....--> >
 
    &lt;ImageView
        

<!--....--> />
 
    &lt;TextView
       

<!--....--> />
&lt;/LinearLayout>
{% endhighlight %}

Si el layout consta de estos dos elementos, Lint muestra el siguiente mensaje : *This tag and its children can be replaced by one <TextView> and a compound drawable*. Viene a decir que es posible simplificar el layout eliminando la imagen y usarla dentro del elemento TextView como **Compound Drawable**.

Como es frecuente en Android, hay dos formas de hacer esto, mediante código o mediante XML. Empecemos con el primero:

El método `setCompoundDrawableWithIntrinsicBounds()`, se encarga de unir un ImageView a un TextView, como menciona su documentación:

> Sets the Drawables (if any) to appear to the left of, above, to the right of, and below the text. Use 0 if you do not want a Drawable there. The Drawables&#8217; bounds will be set to their intrinsic bounds. 

Los cuatro parámetros que acepta este método son las imágenes a adjuntar al texto, se puede adjuntar a la izquierda, arriba, derecha o abajo. Si solo interesa fijar una imagen a la izquierda del texto, basta con pasar un 0 a los 3 parámetros restantes.

Una vez unida la imagen al texto, con `setCompoundDrawablePadding()` se puede establecer un relleno (padding) para separar el texto de la imagen la distancia deseada, por ejemplo.

{% highlight java %}>TextView tv = (TextView) findViewById( R.id.textView );
tv.setCompoundDrawablesWithIntrinsicBounds( R.drawable.ic_launcher, 0, 0, 0 );
tv.setCompoundDrawablePadding(10);
{% endhighlight %}

Es posible realizar el proceso anterior mediante XML, en lugar de java:

{% highlight xml %}>&lt;TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:drawableLeft="@drawable/ic_launcher"
        android:drawablePadding="10dp"
        android:gravity="center_vertical"
        android:text="@string/text"
        android:textAppearance="?android:attr/textAppearanceSmall" />
{% endhighlight %}

Cos los atributos (`android:drawableLeft`, y `android:drawablePadding`) se logra el mismo resultado.

Con esta pequeña optimización estamos reduciendo el layout de dos a un View, puede parecer poco, pero si usamos esto en un listView con 10 filas por ejemplo, se pintará más rápido y el desplazamiento por la lista será más suave.

### Referencias

*setCompoundDrawablesWithIntrinsicBounds() Android Reference* **|** <a href="http://developer.android.com/reference/android/widget/TextView.html#setCompoundDrawablesWithIntrinsicBounds%28int,%20int,%20int,%20int%29" target="_blank">Visitar sitio</a>  
*setCompoundDrawablePadding() Android Reference* **|** <a href="http://developer.android.com/reference/android/widget/TextView.html#setCompoundDrawablePadding%28int%29" target="_blank">Visitar sitio</a>

<div class="sharedaddy">
  <div class="sd-content">
    <ul>
      <li>
        <a class="hastip" rel="nofollow" href="http://twitter.com/home?status=Optimizando la interfaz Android &#8211; Compound Drawables+http://elbauldelprogramador.com/optimizando-la-interfaz-android-compound-drawables/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Twitter" target="_blank"><span class="iconbox-title"><i class="icon-twitter icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="http://www.facebook.com/sharer.php?u=http://elbauldelprogramador.com/optimizando-la-interfaz-android-compound-drawables/&t=Optimizando la interfaz Android &#8211; Compound Drawables+http://elbauldelprogramador.com/optimizando-la-interfaz-android-compound-drawables/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Facebook" target="_blank"><span class="iconbox-title"><i class="icon-facebook icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="https://plus.google.com/share?url=Optimizando la interfaz Android &#8211; Compound Drawables+http://elbauldelprogramador.com/optimizando-la-interfaz-android-compound-drawables/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en G+" target="_blank"><span class="iconbox-title"><i class="icon-google-plus icon-2x"></i></span></a>
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

 [1]: /how-to/adapter-personalizado-en-android/ "Cómo crear un adapter personalizado en Android"
 [2]: /programacion/programacion-android-interfaz-grafica/ "Programación Android: Interfaz gráfica – Conceptos básicos"