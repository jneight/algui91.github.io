---
id: 1941
title: Sincronizar Google Drive en Linux en 4 pasos
author: Alejandro Alcalde
layout: post
guid: http://elbauldelprogramador.com/?p=1941
permalink: /sincronizar-google-drive-en-linux-en-4-pasos/
categories:
  - linux
tags:
  - cliente google drive linux
  - grive
  - tutorial grive
---
Llevaba tiempo buscando la manera de sincronizar los archivos de **Google Drive en Linux** con carpetas locales del mismo modo que Dropbox. Pensé en usar el programa [inotify][1], pero no sabía muy bien por donde empezar. Hace unos días encontré la respuesta en <a href="https://openlinuxforums.org" title="Foro linux" target="_blank">openlinuxforums</a> y al parecer no iba mal encaminado, es una solución bastante sencilla usando inotify y nos permitirá mantener sincronizados los archivos y carpetas de **Google Drive** en todos los ordenadores que queramos.

<span class="highlight style-2">Hay una modificación para este método en <a href="http://elbauldelprogramador.com/linux/sincronizar-google-drive-en-linux-en-4-pasos-actualizacion/" title="Sincronizar Google Drive en Linux en 4 pasos [Actualización]">Sincronizar Google Drive en Linux en 4 pasos [Actualización]</a></span>

<!--more-->

### 1. Preparar el entorno

Es recomendable crear un directorio *bin* para alojar los [scripts][2] necesarios:

{% highlight bash %}>mkdir ~/bin
{% endhighlight %}

Para asegurarse de que la carpeta se ha añadido al PATH cierra sesión y vuelve a entrar, ahora debería aparecer el directorio creado en la variable *PATH*

{% highlight bash %}>echo $PATH
{% endhighlight %}

### 2. Crear la carpeta local a sincronizar

En esta carpeta se descargarán y mantendrán sincronizados los ficheros de Google Drive:

{% highlight bash %}>mkdir ~/Drive
{% endhighlight %}

Ahora instalamos **grive**:

En debian:

{% highlight bash %}>$ sudo apt-get install grive
{% endhighlight %}

En Ubuntu:

{% highlight bash %}>$ sudo add-apt-repository ppa:nilarimogard/webupd8
$ apt-get update
$ apt-get install grive
{% endhighlight %}

Nos autentificamos y otorgamos los permisos necesarios a Grive:

{% highlight bash %}>cd ~/Drive &#038;&#038; grive -a
{% endhighlight %}

El comando de arriba mostrará un link, clica en él y autoriza a Grive para que pueda acceder a **Google Drive**.

### 3. Sincronizar automáticamente los ficheros de la carpeta local

Instala *inotify-tools* si es necesario:

{% highlight bash %}>$ sudo apt-get install inotify-tools
{% endhighlight %}

Ahora crearemos un script que monitorize la carpeta local para detectar cualquier cambio (Script gracias a Peter Österberg, 2012):

{% highlight bash %}>#!/bin/bash
# Google Drive Grive script that syncs your Google Drive folder on change
# This functionality is currently missing in Grive and there are still no
# official Google Drive app for Linux coming from Google.
#
# This script will only detect local changes and trigger a sync. Remote
# changes will go undetected and are probably still best sync on a periodic
# basis via cron.
#
# Kudos to Nestal Wan for writing the excellent Grive software
# Also thanks to Google for lending some free disk space to me
#
# Peter Österberg, 2012
 
GRIVE_COMMAND_WITH_PATH=/usr/bin/grive    # Path to your grive binary, change to match your system
GDRIVE_PATH=~/Drive         # Path to the folder that you want to be synced
TIMEOUT=300              # Timeout time in seconds, for periodic syncs. Nicely pointed out by ivanmacx
 
while true
do
    inotifywait -t $TIMEOUT -e modify -e move -e create -e delete -r $GDRIVE_PATH
    cd $GDRIVE_PATH &#038;&#038; $GRIVE_COMMAND_WITH_PATH
done
{% endhighlight %}

Modifica las rutas conforme a tu configuración y dale permisos de ejecución:

{% highlight bash %}>$ chmod +x grive.sh
{% endhighlight %}

Por último añadelo a las aplicaciones de inicio. Dependiendo de la distribución que uses el método puede variar en Xfce dirígete a Settings » Sesiones e Inicio » Pestaña aplicaciones » ánadir.

### 4. Añadir una entrada a cron para sincronizar los cambios remotos

Hasta el momento, los únicos cambios que se sincronizan son los locales, para lograr detectar cambios en **Google Drive** añadiremos una entrada a [cron][3]. Para ello ejecutamos el comando `crontab -e` y añadimos la siguiente línea:

{% highlight bash %}>*/10 * * * *  cd "$HOME/Drive" &#038;&#038; grive >/dev/null 2>&#038;1
{% endhighlight %}

Con esto *grive* detectará si ha habido cambios en el servidor remoto cada 10 minutos.

### Modificaciones sobre el script original

A los scripts mencionados arriba les hice unas pequeñas modificaciones que muestro a continuación.

Mi entrada en el crontab es la siguiente:

{% highlight bash %}>*/10 * * * * cd "$HOME"/Drive &#038;&#038; grive > /tmp/GRIVE_LOG
{% endhighlight %}

La única diferencia es que guardo la salida del programa en un fichero temporal a modo de log, dentro de poco veremos por qué. 

Yo uso [xmonad][3], y para lograr que grive se ejecute al iniciar sesión añadí una línea al script que es ejecutado cuando me loggeo:

{% highlight bash %}>exec /home/hkr/bin/grive.sh 2>&#038;1 | tee /tmp/GRIVE_LOG &#038;
{% endhighlight %}

De nuevo vuelvo a redirigir la salida del programa a un fichero, esta vez con [tee][4].

Por último, en este mismo archivo, añadí otra línea para que se muestre el fichero que estoy usando como log en el escritorio, y poder así observar si se están sincronizando correctamente los archivos:

{% highlight bash %}>xrootconsole --wrap --bottomup -geometry 233x16+5+570 /tmp/GRIVE_LOG &#038;
{% endhighlight %}

Hace algún tiempo expliqué cómo usar xroot en el artículo [Cómo tener un terminal transparente como wallpaper que muestre información][5]

### Conclusión

Con estos 4 pasos hemos conseguido mantener sincronizado nuestro Google Drive en Linux, y por el camino, al menos yo, he aprendido unas cuantas cosas. ¿Qué os parece?, ¿usáis alguna otra alternativa para lograr el mismo funcionamiento? Dejad un comentario.

El resultado de estas modificaciones es el siguiente:

[<img src="http://elbauldelprogramador.com/content/uploads/2013/11/Sincronizar-Google-Drive-en-Linux-en-4-pasos-1024x575.png" alt="Sincronizar Google Drive en Linux en 4 pasos" width="770" height="432" class="aligncenter size-large wp-image-2013" />][6]{.thumbnail}

#### Referencias

*Howto: Auto-sync Google Drive to a local folder with Grive _ Ubuntu & Debian* **|** <a href="https://openlinuxforums.org/index.php?topic=3144.0" target="_blank">openlinuxforums.org</a>  
*Créditos de la imagen* **|** <a href="https://plus.google.com/+MuktwareMagazine/posts/ZPN9MxuV7VR" target="_blank">plus.google.com</a>

<div class="sharedaddy">
  <div class="sd-content">
    <ul>
      <li>
        <a class="hastip" rel="nofollow" href="http://twitter.com/home?status=Sincronizar Google Drive en Linux en 4 pasos+http://elbauldelprogramador.com/sincronizar-google-drive-en-linux-en-4-pasos/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Twitter" target="_blank"><span class="iconbox-title"><i class="icon-twitter icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="http://www.facebook.com/sharer.php?u=http://elbauldelprogramador.com/sincronizar-google-drive-en-linux-en-4-pasos/&t=Sincronizar Google Drive en Linux en 4 pasos+http://elbauldelprogramador.com/sincronizar-google-drive-en-linux-en-4-pasos/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Facebook" target="_blank"><span class="iconbox-title"><i class="icon-facebook icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="https://plus.google.com/share?url=Sincronizar Google Drive en Linux en 4 pasos+http://elbauldelprogramador.com/sincronizar-google-drive-en-linux-en-4-pasos/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en G+" target="_blank"><span class="iconbox-title"><i class="icon-google-plus icon-2x"></i></span></a>
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

 [1]: http://elbauldelprogramador.com/script/ejecutar-un-script-al-modificar-un-fichero-con-inotify/ "Ejecutar un script al modificar un fichero con inotify"
 [2]: http://elbauldelprogramador.com/category/script/
 [3]: http://elbauldelprogramador.com/how-to/configurar-xmonad-con-trayer-y-fondo-de-pantalla-aleatorio/ "Configurar xmonad con trayer y fondo de pantalla aleatorio"
 [4]: http://elbauldelprogramador.com/script/buscar-archivos-con-locate-mediante-expresiones-regulares-complejas/ "Buscar archivos con locate mediante expresiones regulares"
 [5]: http://elbauldelprogramador.com/how-to/como-tener-un-terminal-transparente-como-wallpaper-que-muestre-informacion/ "Cómo tener un terminal transparente como wallpaper que muestre información"
 [6]: http://elbauldelprogramador.com/content/uploads/2013/11/Sincronizar-Google-Drive-en-Linux-en-4-pasos.png