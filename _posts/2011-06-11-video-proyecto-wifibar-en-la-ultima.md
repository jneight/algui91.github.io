---
id: 221
title: '[Vídeo] Proyecto WifiBar, en la última fase de desarrollo'
author: Alejandro Alcalde
layout: post
guid: http://elbauldelprogramador.org/video-proyecto-wifibar-en-la-ultima-fase-de-desarrollo/
permalink: /video-proyecto-wifibar-en-la-ultima/
blogger_blog:
  - www.elbauldelprogramador.org
  - www.elbauldelprogramador.org
  - www.elbauldelprogramador.org
blogger_author:
  - Alejandro Alcaldehttps://profiles.google.com/117030001562039350135noreply@blogger.com
  - Alejandro Alcaldehttps://profiles.google.com/117030001562039350135noreply@blogger.com
  - Alejandro Alcaldehttps://profiles.google.com/117030001562039350135noreply@blogger.com
blogger_permalink:
  - /2011/06/video-proyecto-wifibar-en-la-ultima.html
  - /2011/06/video-proyecto-wifibar-en-la-ultima.html
  - /2011/06/video-proyecto-wifibar-en-la-ultima.html
categories:
  - android
  - aplicaciones
  - opensource
tags:
  - curso android pdf
---
<img border="0" src="http://elbauldelprogramador.com/content/uploads/2013/07/iconoAndroid.png" style="clear:left; float:left;margin-right:1em; margin-bottom:1em" />

Ya he hablado otras veces sobre este proyecto, que se trata ni más ni menos que del proyecto de fin de curso, [publiqué un vídeo hace algún tiempo][1], pero aún le quedaba mucho por mejorar, sobre todo la interfaz, visualmente parece que el programa no tiene mucho trabajo, pero si que lo tiene, ya que todo se envía y recibe de una base de datos. A continuación voy a explicar un poco su funcionamiento:

  
<!--more-->

La explicación es un poco extensa, pero he querido describir lo mejor posible el funcionamiento de la aplicación.

Toda la información que se muestra en pantalla se recoge de la base de datos que hay en un servidor, al que la aplicación está conectado mediante Wifi.

Como pantalla inicial, tenemos la selección de camarero, donde se selecciona el camarero que va a realizar las comandas sobre las distintas mesas. Despues de seleccionarlo, se abre otra Actividad(Pantalla), en la que se muestran las mesas junto con su estado, que puede ser abierta o cerrada. Si la mesa está abierta, aparecerán además dos botones más, uno para realizar una comanda nueva, y otro para ver las comandas que se han realizado sobre esa mesa.

Si pulsamos en ***Hacer comanda***, se abre otra pantalla en la que seleccionaremos la familia del artículo, y, finalmente el artículo en cuestión.

Cuando se selecciona el artículo, se vuelve a la misma pantalla de hacer comanda, pero esta vez con el artículo añadido, donde podremos fijar la cantidad a pedir, que por defecto es uno. Selecionamos tantos artículos como queramos y estos se irán añadiendo como lineas de comanda. Si nos hemos equivocado, podemos eliminar los artículos que deseemos marcando la casilla de la izquierda y pulsando en el botón rojo. Cuando estemos listos le damos al botón marchar (El de la derecha del todo).

Al darle a marchar, volvemos a la pantalla de selección de mesas, donde podremos seguir abriendo o cerrando mesas, haciendo comandas o recuperando lineas ya enviadas. Mientras vamos haciendo operaciones con la aplicación, se envían y reciben datos de la base de datos, como números de factura, nº de comandas, artículos etc etc.

*Si dispusiéramos de varios terminales, se vería como se van actualizando las mesas que abren o cierran otros camareros.*

Seguimos en la pantalla de selección de mesas, ya se han realizado algunas comandas, y queremos ver en la mesa 1 por ejemplo, que nos han pedido, bien porque tenemos que modificar una cantidad de algún producto, o porque un cliente se ha arrepentido y ahora quiere una Coca cola Zero en vez de una normal, pues todo esto podremos hacerlo desde esta pantalla, modificar o elminar comandas ya enviadas.

Básicamente este es el funcionamiento de la aplicación, en este vídeo no he forzado a que aparezcan los mensajes de error ya que lo hice en el [vídeo anterior][1]. En esta versión hay un nuevo mensajer de error, y es cuando se intenta ver las comandas ya enviadas sobre una mesa que aún no tiene comandas, al pasar esto, se informa con el mensaje: ***&#8220;Esta mesa no tiene comandas aún&#8221;***

Bueno, eso es todo, aún falta hacer una pequeña modificación, que al cerrar una mesa en vez de mostrar el total a pagar, se abra otra ventana en la que se muestre el dinero a pagar, se introduzca el efectivo, y muestre el cambio.



<div class="sharedaddy">
  <div class="sd-content">
    <ul>
      <li>
        <a class="hastip" rel="nofollow" href="http://twitter.com/home?status=[Vídeo] Proyecto WifiBar, en la última fase de desarrollo+http://elbauldelprogramador.com/video-proyecto-wifibar-en-la-ultima/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Twitter" target="_blank"><span class="iconbox-title"><i class="icon-twitter icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="http://www.facebook.com/sharer.php?u=http://elbauldelprogramador.com/video-proyecto-wifibar-en-la-ultima/&t=[Vídeo] Proyecto WifiBar, en la última fase de desarrollo+http://elbauldelprogramador.com/video-proyecto-wifibar-en-la-ultima/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Facebook" target="_blank"><span class="iconbox-title"><i class="icon-facebook icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="https://plus.google.com/share?url=[Vídeo] Proyecto WifiBar, en la última fase de desarrollo+http://elbauldelprogramador.com/video-proyecto-wifibar-en-la-ultima/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en G+" target="_blank"><span class="iconbox-title"><i class="icon-google-plus icon-2x"></i></span></a>
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

 [1]: http://elbauldelprogramador.com/video-demostracion-del-proyecto-wifibar/