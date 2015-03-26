---
id: 2018
title: 'Sincronizar Google Drive en Linux en 4 pasos [Actualización]'
author: Alejandro Alcalde
layout: post
guid: http://elbauldelprogramador.com/?p=2018
permalink: /sincronizar-google-drive-en-linux-en-4-pasos-actualizacion/
categories:
  - linux
tags:
  - cliente google drive linux
  - grive
  - tutorial grive
---
Hace poco vimos en un artículo cómo [Sincronizar Google Drive en Linux en 4 pasos][1]. Llevo usando ese método unas semanas y hasta ahora todo funcionaba correctamente. Sin embargo me he dado cuenta que cuando se usa con archivos muy grandes puede haber problemas, ya que *grive* vuelve a ejecutarse varias veces mientras está subiendo archivos, con lo cual acaban pasando cosas extrañas, como quedarse subiendo el archivo indefinidamente o inundar la memoria RAM. Aplicar los siguientes cambios parece que soluciona los problemas.

<!--more-->

### El problema

Tal y como se explicó en el artículo anterior, se tiene un [script][2] encargado de detectar los cambios locales con [inotify][3] y una entrada en <a href="http://es.wikipedia.org/wiki/Cron_%28Unix%29" title="Cron wikipedia" target="_blank">cron</a> ejecutando *grive* cada X tiempo para descargar los cambios remotos. Por tanto, puede ocurrir que en un determinado instante grive se esté ejecutando más de una vez, y si está realizando alguna operación de descarga o subida *inotify* detectará que los ficheros están cambiando y volverá a lanzar *grive*. 

Es decir, si estamos subiendo/descargando un archivo lo bastante grande para que tarde más que los periodos de pausa que hay en el script y en cron, el comportamiento de *grive* no será el correcto porque los archivos están cambiando continuamente mientras están siendo descargados.

### Posible solución

Tras haber hecho varias pruebas, una solución que parece ser correcta es la siguiente:

  * Modificar la entrada en cron para *grive*
  * Modificar el script para que compruebe si hay alguna instancia de *grive* en ejecución, y, en ese caso, esperar a que termine.

El script quedaría así:

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
 
GRIVE_COMMAND_WITH_PATH=/usr/bin/grive   # Path to your grive binary, change to match your system
GDRIVE_PATH=~/Drive                      # Path to the folder that you want to be synced
TIMEOUT=60               # Timeout time in seconds, for periodic syncs. Nicely pointed out by ivanmacx

declare -i esta_grive_ejecutando

while true
do
    inotifywait -t 300 -e modify -e move -e create -e delete -r $GDRIVE_PATH
    esta_grive_ejecutando=`pidof grive`
    if [[ "$esta_grive_ejecutando" -ne 0 ]] 
    then
        echo "Grive está ejecutandose, PID: $esta_grive_ejecutando. Esperando..."
        sleep $TIMEOUT
    else
        cd $GDRIVE_PATH &#038;&#038; $GRIVE_COMMAND_WITH_PATH
    fi
done
{% endhighlight %}

Con esta pequeña modificación logramos que no haya varias instancias de *grive* en ejecución. Además podríamos dejar la entrada en cron, ya que de esta forma si cron está ejecutando *grive*, el script no lo hará, porque detecta que ya hay una instancia en ejecución. 

### Script para CRON

Sería muy similar, salvo que no monitoriza el estado del directorio con inotify, de esta forma obtendremos los cambios remotos:

{% highlight bash %}>#!/bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

TIMEOUT=60

declare -i is_grive_cron_running

is_grive_cron_running=`pidof grive`

if [[ "$is_grive_cron_running" -ne 0 ]] 
then
    echo "Grive está corriendo en cron: $is_grive_cron_running. Esperando..."
    sleep $TIMEOUT
else
    cd "$HOME/Drive" &#038;&#038; grive | tee /tmp/GRIVE_LOG
fi
{% endhighlight %}

Lo guardamos como *update-grive.sh*, le damos permisos de ejecución `chmod +x update-grive.sh` y lo añadimos a crontab:

{% highlight bash %}>*/10 * * * *  /home/hkr/bin/update-grive.sh
{% endhighlight %}

#### Referencias

*Howto: Auto-sync Google Drive to a local folder with Grive _ Ubuntu & Debian* **|** <a href="https://openlinuxforums.org/index.php?topic=3144.0" target="_blank">openlinuxforums.org</a> 

<div class="sharedaddy">
  <div class="sd-content">
    <ul>
      <li>
        <a class="hastip" rel="nofollow" href="http://twitter.com/home?status=Sincronizar Google Drive en Linux en 4 pasos [Actualización]+http://elbauldelprogramador.com/sincronizar-google-drive-en-linux-en-4-pasos-actualizacion/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Twitter" target="_blank"><span class="iconbox-title"><i class="icon-twitter icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="http://www.facebook.com/sharer.php?u=http://elbauldelprogramador.com/sincronizar-google-drive-en-linux-en-4-pasos-actualizacion/&t=Sincronizar Google Drive en Linux en 4 pasos [Actualización]+http://elbauldelprogramador.com/sincronizar-google-drive-en-linux-en-4-pasos-actualizacion/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Facebook" target="_blank"><span class="iconbox-title"><i class="icon-facebook icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="https://plus.google.com/share?url=Sincronizar Google Drive en Linux en 4 pasos [Actualización]+http://elbauldelprogramador.com/sincronizar-google-drive-en-linux-en-4-pasos-actualizacion/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en G+" target="_blank"><span class="iconbox-title"><i class="icon-google-plus icon-2x"></i></span></a>
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

 [1]: http://elbauldelprogramador.com/linux/sincronizar-google-drive-en-linux-en-4-pasos/ "Sincronizar Google Drive en Linux en 4 pasos"
 [2]: http://elbauldelprogramador.com/category/script/
 [3]: http://elbauldelprogramador.com/script/ejecutar-un-script-al-modificar-un-fichero-con-inotify/ "Ejecutar un script al modificar un fichero con inotify"