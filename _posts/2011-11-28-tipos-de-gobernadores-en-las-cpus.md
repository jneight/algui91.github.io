---
id: 301
title: Tipos de gobernadores en las CPUs
author: Alejandro Alcalde
layout: post
guid: http://elbauldelprogramador.org/tipos-de-gobernadores-en-las-cpus/
permalink: /tipos-de-gobernadores-en-las-cpus/
blogger_blog:
  - www.elbauldelprogramador.org
  - www.elbauldelprogramador.org
blogger_author:
  - Alejandro Alcaldehttps://profiles.google.com/117030001562039350135noreply@blogger.com
  - Alejandro Alcaldehttps://profiles.google.com/117030001562039350135noreply@blogger.com
blogger_permalink:
  - /2011/11/tipos-de-gobernadores-en-las-cpus.html
  - /2011/11/tipos-de-gobernadores-en-las-cpus.html
share_data:
  - '[]'
  - '[]'
share_all_data:
  - '{"like_count":"0","share_count":"0","twitter":0,"plusone":1,"stumble":0,"pinit":0,"count":1,"time":1333551779}'
  - '{"like_count":"0","share_count":"0","twitter":0,"plusone":1,"stumble":0,"pinit":0,"count":1,"time":1333551779}'
share_count:
  - 0
  - 0
categories:
  - android
  - aplicaciones
tags:
  - curso android pdf
  - que es cpu conservative
  - samsung galaxy scl gti9003
---
<div class="separator" style="clear: both; text-align: center;">
  <a href="http://1.bp.blogspot.com/-KQUYsW_nayM/TtPuH21UIaI/AAAAAAAAB2o/KsZQh-2hjtk/s1600/gpu.jpg" imageanchor="1" style="clear:left; float:left;margin-right:1em; margin-bottom:1em"><img border="0" height="150" width="200" src="http://1.bp.blogspot.com/-KQUYsW_nayM/TtPuH21UIaI/AAAAAAAAB2o/KsZQh-2hjtk/s200/gpu.jpg" /></a>
</div>

Para los que tengáis vuestro terminal [rooteado][1] voy a hablar en estos días de dos aplicaciones que pueden ayudarnos a extender el tiempo de vida de la batería. Pero antes de escribir estos pequeños manuales de configuración de las aplicaciones (que son SetCPU y CPU Tunner), tengo que introducir un concepto de los microprocesadores llamado gobernadores (governors):

Lo que hacen los gobernadores es definir unas reglas de cambio de frecuencias en el micro, ya sea una velocidad de reloj mayor o menor, y cuando han de cambiarse estas frecuencias.

El gobernador define las características de energía de la CPU del sistema que a su vez afectan el rendimiento de la CPU. Cada gobernador tiene su propia conducta, propósito e idoneidad en términos de carga de trabajo.

  
<!--more-->

La frecuencia a la que una CPU puede operar viene limitada por su diseño. A menudo, una CPU solo puede funcionar en un número determinado de frecuencias discretas. Por ejemplo en mi Galaxy S son cuatro frecuencias (300mHz, 600mHz, 800mHz y 1000mHz). También, los valores de los parametros **scaling\_max\_freq y scaling\_min\_freq** se fijan por defecto a las frecuencias máximas y mínimas disponibles en la CPU. Para elegir bien un gobernador, tenemos que tener en cuenta la carga de trabajo a la que se va a someter a la CPU. A continuación voy a explicar por encima la función de cada gobernador:

***Performance:*** En este gobernanor solo se considera el rendimiento, fuerza a la CPU a ejecutarse en la frecuencia más alta (**scaling\_max\_freq**).

***ondemand:*** La principal consideración es la adaptación a la carga actual. Comprueba la carga regularmente, cuando ésta sobrepasa el umbral máximo (**up_threshold**) la CPU se ejecuta a la frecuencia máxima. Cuando la carga cae por debajo del umbral, se ajusta la frecuencia de la CPU a la siguiente más baja. Este gobernador causa menos latencia que el conservative.

***conservative:***La princpial consideración es la adaptación más cercana a la carga actual. Al igual que el gobernador Ondemand, el gobernador Conservative ajusta la frecuencia de reloj según el uso. Sin embargo, mientras el gobernador Ondemand lo hace de una manera agresiva (es decir, desde lo máximo a lo mínimo y viceversa), el gobernador Conservative cambia de frecuencias gradualmente. El gobernador Conservative se ajustará a una frecuencia de reloj que estime correcta para la carga, en lugar de elegir simplemente entre máxima y mínima, es decir, cuando la carga supera el umbral máximo (**up_threshold**), se ajusta la frecuencia de la CPU a la siguiente más alta. Cuando la carga cae por debajo del umbral (**down_threshold**) se ajusta la frecuencia a la siguiente más baja. Aunque esto podría proporcionar un significativo ahorro en consumo de energía, lo hace siempre a una latencia mayor que la del gobernador Ondemand.

***userspace:*** Se deja el control a los programas en el espacio del usuario. Se ajusta la frecuencia al valor especificado por el programa en el espacio de usuario (mediante el uso del parámetro **scaling_setspeed**). De todos lo gobernadores, Userspace es el más adaptable; y dependiendo de cómo se configure, puede ofrecer el mejor balance entre rendimiento y consumo para su sistema.

Fuentes: <a target="_blank" href="http://publib.boulder.ibm.com/infocenter/lnxinfo/v3r0m0/index.jsp?topic=%2Fliaai%2Fcpufreq%2FUnderstandingCPUFreqSubsystem.htm">publib.boulder.ibm.com</a> y <a target="_blank" href="http://docs.fedoraproject.org/es-ES/Fedora/14/html/Power_Management_Guide/cpufreq_governors.html#governor_types">docs.fedoraproject.org</a>

<div class="sharedaddy">
  <div class="sd-content">
    <ul>
      <li>
        <a class="hastip" rel="nofollow" href="http://twitter.com/home?status=Tipos de gobernadores en las CPUs+http://elbauldelprogramador.com/tipos-de-gobernadores-en-las-cpus/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Twitter" target="_blank"><span class="iconbox-title"><i class="icon-twitter icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="http://www.facebook.com/sharer.php?u=http://elbauldelprogramador.com/tipos-de-gobernadores-en-las-cpus/&t=Tipos de gobernadores en las CPUs+http://elbauldelprogramador.com/tipos-de-gobernadores-en-las-cpus/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Facebook" target="_blank"><span class="iconbox-title"><i class="icon-facebook icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="https://plus.google.com/share?url=Tipos de gobernadores en las CPUs+http://elbauldelprogramador.com/tipos-de-gobernadores-en-las-cpus/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en G+" target="_blank"><span class="iconbox-title"><i class="icon-google-plus icon-2x"></i></span></a>
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

 [1]: /2011/09/rootear-samsung-galaxy-s-gt-i9003.html