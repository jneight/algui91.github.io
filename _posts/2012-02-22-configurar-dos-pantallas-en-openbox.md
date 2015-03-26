---
id: 343
title: Configurar dos pantallas en OpenBox bajo CruchBang y wallpaper aleatorio
author: Alejandro Alcalde
layout: post
guid: http://elbauldelprogramador.org/configurar-dos-pantallas-en-openbox-bajo-cruchbang-y-wallpaper-aleatorio/
permalink: /configurar-dos-pantallas-en-openbox/
blogger_blog:
  - www.elbauldelprogramador.org
  - www.elbauldelprogramador.org
blogger_author:
  - Alejandro Alcaldehttps://profiles.google.com/117030001562039350135noreply@blogger.com
  - Alejandro Alcaldehttps://profiles.google.com/117030001562039350135noreply@blogger.com
blogger_permalink:
  - /2012/02/configurar-dos-pantallas-en-openbox.html
  - /2012/02/configurar-dos-pantallas-en-openbox.html
share_data:
  - '[]'
  - '[]'
share_all_data:
  - '{"like_count":"0","share_count":"0","twitter":0,"plusone":0,"stumble":0,"pinit":0,"count":0,"time":1333551703}'
  - '{"like_count":"0","share_count":"0","twitter":0,"plusone":0,"stumble":0,"pinit":0,"count":0,"time":1333551703}'
share_count:
  - 0
  - 0
categories:
  - aplicaciones
  - curiosidades
  - opensource
  - script
tags:
  - configuracion openbox
  - crunchbang cambiar wallpaper
  - debian openbox distro
  - openbox
  - wallpaper para openbox
---
<div class="separator" style="clear: both; text-align: center;">
  <a href="http://1.bp.blogspot.com/-iiunZ-gX5Y8/T0TbVE86pHI/AAAAAAAACGw/wmYeZWkIe-s/s1600/1329912632_stock_connect.png" imageanchor="1" style="clear:left; float:left;margin-right:1em; margin-bottom:1em"><img border="0" height="128" width="128" src="http://1.bp.blogspot.com/-iiunZ-gX5Y8/T0TbVE86pHI/AAAAAAAACGw/wmYeZWkIe-s/s400/1329912632_stock_connect.png" /></a>
</div>

Llevaba tiempo queriendo instalar en mi equipo la distribución <a target="_blank" href="http://crunchbanglinux.org/">CrunchBang</a>, que es una distro muy ligera basada en debian que viene con <a target="_blank" href="http://openbox.org/">openbox</a>, este fin de semana finalmente me decidí a instalarla para probarla y la he dejado ya que me ha gustado bastante por si simpleza y capacidad de configuración.

Encontré un pequeño problema al instalarla, y era que al tener dos pantallas conectadas al pc, por defecto las clonaba, es decir, que aparecía lo mismo en las dos pantallas. Cuando cambiaba la configuración para mostrarlas como dos pantallas independientes todo iba bien, pero al reiniciar volvía a clonarlas.

  
<!--more-->

Después de un poco de búsqueda por la red encontré solución al problema, usando el comando xrandr de la siguiente manera:

{% highlight bash %}>xrandr --output DVI-I-1 --mode 1280x1024 --right-of DVI-I-2
{% endhighlight %}

Con esto estamos diciendo que la pantalla DVI-I-1 estará a la derecha la pantalla DVI-I-2

El siguiente paso es hacer que este comando se ejecute siempre al iniciar el pc, para ello tenemos que modificar el archivo de autoarranque de OpenBox, que se llama *autostart* y suele estar en *~/.config/openbox*. Añadimos la siguiente línea debajo de *lxsession &*:

{% highlight bash %}>xrandr --output DVI-I-1 --mode 1280x1024 --right-of DVI-I-2 &#038;
{% endhighlight %}

De esta forma lo tenemos todo solucionado.

#### Fondos de pantalla aleatorios

Para lograr esto usé un script que encontré en la red hace tiempo y lo modifiqué para adaptarlo a openbox, con la particularidad de que aplico un fondo de pantalla distinto y seleccionado aleatoriamente para cada una de las pantallas. El script en cuestión es el siguiente:

{% highlight bash %}>#!/bin/bash
picsfolder=$HOME"/Ruta/Imagenes/"
bgSaved=$HOME"/.config/nitrogen/bg-saved.cfg"

cd $picsfolder

files=(*.jpg)

N=${#files[@]}

((N=RANDOM%N))
randomfile1=${files[$N]}
#echo $randomfile
((N=RANDOM%N))
randomfile2=${files[$N]}



cat &lt;&lt;&lt;"[:0.0]
file=/usr/share/backgrounds/transparent--i.e-solid-colour.png
mode=1
bgcolor=#252627

[xin_0]
file=$picsfolder$randomfile1
mode=4
bgcolor=#000000

[xin_1]
file=$picsfolder$randomfile2
mode=4
bgcolor=#000000" > $bgSaved
{% endhighlight %}

La variable *picsfolder* es el directorio donde residen las imágenes. *bsSaved* es un archivo de configuración que almacena los datos del último fondo de pantalla que se estableció, y que modificaremos con los nuevos fondos.

Las siguientes líneas seleccionan dos imágenes aleatórias y una vez seleccionadas modificamos el archivo bg-saved.cfg para establecer nuestros fondos de pantalla aleatórios.

Para conseguir que esto funcione debemos volver a modificar el archivo *autostart* de openbox, colocando las siguientes líneas (En este caso debajo del comando xrandr):

{% highlight bash %}>## Set desktop wallpaper
/home/hkr/Pictures/wall_aleatorio.sh
nitrogen --restore &#038;{% endhighlight %}

Os dejo una captura:

<div class="separator" style="clear: both; text-align: center;">
  <a href="http://1.bp.blogspot.com/-Babpz4m6FG4/T0T5AMLSbsI/AAAAAAAACHA/P7YObMPNGM4/s1600/Screenshot%2B-%2B02222012%2B-%2B02%253A43%253A23%2BPM.png" imageanchor="1" style="margin-left:1em; margin-right:1em"><img border="0" height="160" width="400" src="http://1.bp.blogspot.com/-Babpz4m6FG4/T0T5AMLSbsI/AAAAAAAACHA/P7YObMPNGM4/s400/Screenshot%2B-%2B02222012%2B-%2B02%253A43%253A23%2BPM.png" /></a>
</div>

<div class="sharedaddy">
  <div class="sd-content">
    <ul>
      <li>
        <a class="hastip" rel="nofollow" href="http://twitter.com/home?status=Configurar dos pantallas en OpenBox bajo CruchBang y wallpaper aleatorio+http://elbauldelprogramador.com/configurar-dos-pantallas-en-openbox/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Twitter" target="_blank"><span class="iconbox-title"><i class="icon-twitter icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="http://www.facebook.com/sharer.php?u=http://elbauldelprogramador.com/configurar-dos-pantallas-en-openbox/&t=Configurar dos pantallas en OpenBox bajo CruchBang y wallpaper aleatorio+http://elbauldelprogramador.com/configurar-dos-pantallas-en-openbox/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Facebook" target="_blank"><span class="iconbox-title"><i class="icon-facebook icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="https://plus.google.com/share?url=Configurar dos pantallas en OpenBox bajo CruchBang y wallpaper aleatorio+http://elbauldelprogramador.com/configurar-dos-pantallas-en-openbox/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en G+" target="_blank"><span class="iconbox-title"><i class="icon-google-plus icon-2x"></i></span></a>
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