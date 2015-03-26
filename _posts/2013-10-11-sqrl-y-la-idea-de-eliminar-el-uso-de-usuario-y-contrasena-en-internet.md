---
id: 1929
title: SQRL y la idea de eliminar el uso de usuario y contraseña en internet
author: Alejandro Alcalde
layout: post
guid: http://elbauldelprogramador.com/?p=1929
permalink: /sqrl-y-la-idea-de-eliminar-el-uso-de-usuario-y-contrasena-en-internet/
categories:
  - Security Now!
tags:
  - metodos de autenticación
  - metodos de autentificacion
  - security now SQRL
  - SQRL
  - SQRL steve gibson
  - uso de usuario y contraseña
---
Los lectores habituales sabrán que suelo escuchar el programa de radio *[Security Now!][1]*, la semana pasada, **Steve Gibson**, uno de los mayores expertos en seguridad anunció que se le había ocurrido una manera de eliminar la necesidad de usar usuario y contraseña para identificarse en los sitios web, eliminando así los problemas que esto conlleva. Steve ha llamado a su invención **SQRL** (*Secure [QR][2] Login*) y ha tenido bastante éxito en la comunidad, tanto que hasta el [W3][3] se ha puesto en contacto con él mostrando interés en este nuevo método de autentificación.

<!--more-->

<img src="http://elbauldelprogramador.com/content/uploads/2013/10/sqrl-login-sample.png" alt="SQRL y la idea de elminar el uso de usuario y contraseña en internet" width="400" height="199" class="thumbnail aligncenter size-full wp-image-1936" />

El sistema **SQRL** es bastante simple, cuando se desea identificarse en un sitio web mediante **SQRL**, aparecerá un código, entonces:

  * El usuario ejecuta en su teléfono móvil la aplicación **SQRL**, y escanea el código mostrado. (O un usuario navegando desde su móvil toca el código. O un usuario de PC hace click en el código.)
  * Para verificar, la aplicación **SQRL** muestra el nombre del dominio contenido en el código **SQRL**.
  * Tras verificar el dominio, el usuario permite a la aplicación **SQRL** que autentifique su identidad.
  * Dejando la información de login en blanco (No es necesario rellenar los campos usuario y contraseña) se hace click en *login*&#8230; y estaremos en nuestra cuenta.

Este método, a pesar de ser bastante simple, es de lejos mucho más seguro que cualquier otra solución de logueo. Intentaré explicar un poco por encima el proceso traduciéndolo de la página de Steve (1), donde hay muchos más detalles.

## ¿Qué ocurre detrás de la escena?

*Gracias a [Luzcila][4] por traducir esta sección*

(Lo escrito a continuación pretende dar al lector una noción general del proceso de autentificación mediante **SQRL**, para detalles más técnicos se puede visitar el enlace de las referencias (1), si hay algún interesado en traducir esta información al castellano, puede contactar conmigo mediante el [formulario de contacto][5] y encantado añadiré el contenido mencionando a su autor).

  * El código QR presentado cerca de los campos de login contiene la URL del servicio de autenticación para el sitio. La URL incluye un número aleatorio largo generado de forma segura por lo que cada presentación de la página de login muestra un código QR diferente. (En el mundo de la criptografía este número aleatorio se conoce como “nonce”)
  * La aplicación de autenticación SQRL de los smartphones encripta el nombre de dominio del sitio indexado por la clave maestra del usuario para producir un par de clave pública específica del sitio (*site-specific public key pair*)
  * La aplicación firma criptográficamente la URL completa contenida en el código QR usando la clave privada específica del sitio. Dado que la URL incluye un número largo aleatorio (el nonce), la firma es única para este sitio y código QR.
  * La aplicación emite una consulta HTTPS POST a la URL del código QR, la cual es del servicio de autenticación del sitio. El POST provee la clave pública específica del sitio y la firma criptográfica que se corresponde de la URL del código QR.
  * El sitio web de autenticación recibe y reconoce la consulta POST devolviéndole una respuesta standard HTTP “200 OK” sin otro contenido. La aplicación SQRL acepta la solicitud exitosa del código QR del usuario identificado.
  * El sitio de autenticación tiene la URL que contiene el nonce que devolvió la página de login del smartphone del usuario. Tiene también una firma criptográfica de esa URL, y la clave pública específica del sitio del usuario. Usa la clave pública para verificar que la firma es válida para la URL. Esto confirma que el usuario que produjo la firma usó la clave privada que corresponde a la clave pública. Tras de verificar la firma, el sitio de autentificación reconoce al usuario (ahora-autenticado) por la clave pública específica de sitio.

<img src="http://elbauldelprogramador.com/content/uploads/2013/10/sign-algo.png" alt="SQRL y la idea de elminar el uso de usuario y contraseña en internet" width="580" height="194" class="thumbnail aligncenter size-full wp-image-1935" />

Resumiendo: &#8220;El login del sitio web presenta un código QR que contiene la URL de su servicio de autenticación, más un nonce. El smartphone del usuario firma la login URL usando una clave privada derivada de su secreto maestro y el nombre de dominio de la URL. El smartphone envía la clave pública que se corresponde para identificar el usuario, y la firma para autenticarlo.&#8221;

A continuación dejo los dos podcast en los que Steve Gibson ha dado cantidad de detalles de cómo funciona el sistema **SQRL**, y en las referencias todos los detalles técnicos. Vuelvo a comentar que toda colaboración para traducir dicha página es bienvenida.

<span class='embed-youtube' style='text-align:center; display: block;'></span>  
<span class='embed-youtube' style='text-align:center; display: block;'></span>

#### Referencias

*(1) SQRL en la página de su creador, Steve* **|** <a href="https://www.grc.com/sqrl/sqrl.htm" target="_blank">grc.com</a> 

<div class="sharedaddy">
  <div class="sd-content">
    <ul>
      <li>
        <a class="hastip" rel="nofollow" href="http://twitter.com/home?status=SQRL y la idea de eliminar el uso de usuario y contraseña en internet+http://elbauldelprogramador.com/sqrl-y-la-idea-de-eliminar-el-uso-de-usuario-y-contrasena-en-internet/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Twitter" target="_blank"><span class="iconbox-title"><i class="icon-twitter icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="http://www.facebook.com/sharer.php?u=http://elbauldelprogramador.com/sqrl-y-la-idea-de-eliminar-el-uso-de-usuario-y-contrasena-en-internet/&t=SQRL y la idea de eliminar el uso de usuario y contraseña en internet+http://elbauldelprogramador.com/sqrl-y-la-idea-de-eliminar-el-uso-de-usuario-y-contrasena-en-internet/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Facebook" target="_blank"><span class="iconbox-title"><i class="icon-facebook icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="https://plus.google.com/share?url=SQRL y la idea de eliminar el uso de usuario y contraseña en internet+http://elbauldelprogramador.com/sqrl-y-la-idea-de-eliminar-el-uso-de-usuario-y-contrasena-en-internet/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en G+" target="_blank"><span class="iconbox-title"><i class="icon-google-plus icon-2x"></i></span></a>
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

 [1]: elbauldelprogramador.com/articulos/security-now-articulos/ "Artículos sobre Security now!"
 [2]: http://elbauldelprogramador.com/articulos/estructura-y-seguridad-de-los-qr-codes/ "Estructura y seguridad de los QR Codes"
 [3]: http://www.w3.org/ "W3 org"
 [4]: http://elbauldelprogramador.com/author/luzcila/ "Artículos de Luzcila"
 [5]: http://elbauldelprogramador.com/contacto/ "Contacto"