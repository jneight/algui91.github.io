---
id: 164
title: Programa que envía mensajes desde Android a PC (Mejora I)
author: Alejandro Alcalde
layout: post
guid: http://elbauldelprogramador.org/programa-que-envia-mensajes-desde-android-a-pc-mejora-i/
permalink: /programa-que-envia-mensajes-desde-2/
blogger_blog:
  - www.elbauldelprogramador.org
  - www.elbauldelprogramador.org
  - www.elbauldelprogramador.org
blogger_author:
  - Alejandro Alcaldehttps://profiles.google.com/117030001562039350135noreply@blogger.com
  - Alejandro Alcaldehttps://profiles.google.com/117030001562039350135noreply@blogger.com
  - Alejandro Alcaldehttps://profiles.google.com/117030001562039350135noreply@blogger.com
blogger_permalink:
  - /2011/04/programa-que-envia-mensajes-desde.html
  - /2011/04/programa-que-envia-mensajes-desde.html
  - /2011/04/programa-que-envia-mensajes-desde.html
categories:
  - android
  - aplicaciones
  - opensource
tags:
  - curso android pdf
---
<img border="0" src="http://elbauldelprogramador.com/content/uploads/2013/07/iconoAndroid.png" style="clear:left; float:left;margin-right:1em; margin-bottom:1em" />  
Como ya comenté en una entrada hace varios días, necesito hacer una [aplicación para android que se comunique con el PC por red][1], y he seguido mejorandola ya que aún está muy limitada, le he hecho algunos cambios, estos cambios son:  
<!--more-->

Al ejecutar la aplicación en el teléfono, se conecta al servidor ejecutandose en el pc que está esperando una conexión.

Ya no pasa lo que pasaba en la anterior versión de la aplicación, que cada vez que se pulsaba el botón enviar, se establecía la conexión con el servidor, se enviaba el mensaje y se cerraba la conexión, ahora la conexión se mantiene activa mientras el programa se esté ejecutando, lo que reduce el tiempo de envio del mensaje, ya que nos ahorramos tener que conectar y desconectar del server por cada mensaje que enviemos.

La aplicación dispone de un checkBox que activa o desactiva el TextView y el botón. (Pero no cierra la conexión con el server.)

* * *

Las mejoras que ahora quiero realizar son:

Que el server soporte varias conexiones de varios clientes, para ello tendré que usar un thread (hilo), que sea el que encargue de la conexión con cada cliente.

Al checkar o des-checkear el checkBox (valga la redundancia <img src="http://elbauldelprogramador.com/wordpress/wp-includes/images/smilies/icon_wink.gif" alt=";)" class="wp-smiley" /> ), se debe conectar o desconectar realmente del server.

Mejorar el diseño, evidentemente la aplicación no va a ser así de simple, pero mientras programo la parte de la conexión esa me vale.

Dejo el código fuente y algunas capturas:  
Parte cliente:



Parte servidor, esta parte está tal y como la [descargué de casiDiablo][2], pero en la siguiente modificación del programa ya la trastearé.



Por ultimo algunas capturas:

<div class="separator" style="clear: both; text-align: center;">
  <a href="http://2.bp.blogspot.com/-fd7bF2KeVz0/TZy69gP91qI/AAAAAAAAAX8/TS2lEDPa5Hc/s1600/cliente.png" imageanchor="1" style="margin-left:1em; margin-right:1em"><img border="0" height="256" width="320" src="http://2.bp.blogspot.com/-fd7bF2KeVz0/TZy69gP91qI/AAAAAAAAAX8/TS2lEDPa5Hc/s320/cliente.png" /></a>
</div>

<div class="separator" style="clear: both; text-align: center;">
  <a href="http://2.bp.blogspot.com/-fmPuUA9j_z0/TZy6-l2N5CI/AAAAAAAAAYE/XqlyacBVbbM/s1600/no.png" imageanchor="1" style="margin-left:1em; margin-right:1em"><img border="0" height="256" width="320" src="http://2.bp.blogspot.com/-fmPuUA9j_z0/TZy6-l2N5CI/AAAAAAAAAYE/XqlyacBVbbM/s320/no.png" /></a>
</div>

<div class="separator" style="clear: both; text-align: center;">
  <a href="http://1.bp.blogspot.com/-WK1A6yIyLPA/TZy6_DB-rGI/AAAAAAAAAYM/GFY0IPD7lYQ/s1600/otra.png" imageanchor="1" style="margin-left:1em; margin-right:1em"><img border="0" height="256" width="320" src="http://1.bp.blogspot.com/-WK1A6yIyLPA/TZy6_DB-rGI/AAAAAAAAAYM/GFY0IPD7lYQ/s320/otra.png" /></a>
</div>

<div class="sharedaddy">
  <div class="sd-content">
    <ul>
      <li>
        <a class="hastip" rel="nofollow" href="http://twitter.com/home?status=Programa que envía mensajes desde Android a PC (Mejora I)+http://elbauldelprogramador.com/programa-que-envia-mensajes-desde-2/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Twitter" target="_blank"><span class="iconbox-title"><i class="icon-twitter icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="http://www.facebook.com/sharer.php?u=http://elbauldelprogramador.com/programa-que-envia-mensajes-desde-2/&t=Programa que envía mensajes desde Android a PC (Mejora I)+http://elbauldelprogramador.com/programa-que-envia-mensajes-desde-2/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Facebook" target="_blank"><span class="iconbox-title"><i class="icon-facebook icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="https://plus.google.com/share?url=Programa que envía mensajes desde Android a PC (Mejora I)+http://elbauldelprogramador.com/programa-que-envia-mensajes-desde-2/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en G+" target="_blank"><span class="iconbox-title"><i class="icon-google-plus icon-2x"></i></span></a>
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

 [1]: http://elbauldelprogramador.com/programa-que-envia-mensajes-desde/
 [2]: http://casidiablo.net/java-socket-chat-basico/