---
id: 1583
title: Resaltar sintaxis del código fuente en LaTeX con minted
author: Alejandro Alcalde
layout: post
guid: http://elbauldelprogramador.com/?p=1583
permalink: /resaltar-sintaxis-del-codigo-fuente-en-latex-con-minted/
categories:
  - LaTeX
tags:
  - ejemplos minted
  - instalar minted
  - pygments
  - resaltar sintaxis codigo latex
---
Hace unas semanas que aprendí a usar <img src="//s0.wp.com/latex.php?latex=%5CLaTeX&#038;bg=ffffff&#038;fg=000&#038;s=0" alt="&#92;LaTeX" title="&#92;LaTeX" class="latex" />, y cada vez me gusta más, proporciona una calidad a los documentos impecable. De hecho, estoy entregando las prácticas de la facultad en <img src="//s0.wp.com/latex.php?latex=%5CLaTeX&#038;bg=ffffff&#038;fg=000&#038;s=0" alt="&#92;LaTeX" title="&#92;LaTeX" class="latex" /> y he reescrito el [Curso de programación Android][1] por completo.

Sin embargo, una de las cosas que más me ha costado conseguir es encontrar alguna forma que me gustase de resaltar la sintaxis en latex del código fuente. Tras mucho buscar por internet encontré un paquete que concluyó con mi búsqueda, se llama **minted**.  
  
<!--more-->

### Instalando dependencias

Para instalarlo, es necesaria una versión de python igual o superior a la 2.6, y *Pygments*. Para instalar el último ejecuta:

{% highlight bash %}># easy_install Pygments
{% endhighlight %}

Si no tienes instalado el programa *easy_install*, ejecuta:

{% highlight bash %}># aptitude install python-setuptools
{% endhighlight %}

### Instalar minted

Descarga el paquete desde su <a href="http://code.google.com/p/minted/downloads/list" target="_blank">repositorio</a>. Extráelo y sitúate en su directorio. Luego ejecuta la instrucción *make*.

### Algunos ejemplos

Ya está todo listo para usar, empecemos con un ejemplo básico extraido del manual, disponible para descargar en las referencias:

{% highlight latex %}>\documentclass{article}

\usepackage{minted}

\begin{document}

\begin{minted}{c}
    /**
    * Ejemplo resaltado sintaxis con minted
    */
    int main() {
        printf("hello, world");
        return 0;
    }
\end{minted}
\end{document}
{% endhighlight %}

Este trozo de código dará como resultado lo siguiente:  
<img src="http://elbauldelprogramador.com/content/uploads/2013/05/mintedEjemploC.png" alt="Ejemplo minted C" width="599" height="246" class="aligncenter size-full wp-image-1587" />

### Insertar código desde un archivo de código fuente

Normalmente, si tenemos un código fuente con muchas líneas es más cómodo incluirlo directamente en <img src="//s0.wp.com/latex.php?latex=%5CLaTeX&#038;bg=ffffff&#038;fg=000&#038;s=0" alt="&#92;LaTeX" title="&#92;LaTeX" class="latex" /> en lugar de copiar todas esas líneas. **Minted** proporciona un comando para tal fin. *\newmintedfile[]{}*. Veamos un ejemplo:

{% highlight latex %}>\newmintedfile[myJava]{java}{
    linenos,
    numbersep=5pt,
    gobble=0,
    frame=lines,
    framesep=2mm,
}
{% endhighlight %}

Con este comando, hemos definido una nueva función (*myJava*), que permitirá incluir el código fuente de un archivo al documento pdf. Por ejemplo. Supongamos que el contenido del fichero *miCodigo.java* es el siguiente:

{% highlight java %}>package com.elbauldelprogramador.actividades;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.TextView;

public class Activity1 extends Activity {
   /** Called when the activity is first created. */
   @Override
   public void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
      setContentView(R.layout.segunda_actividad);
      
      // Capturamos los objetos gráficos que vamos a usar
      TextView text = (TextView) findViewById(R.id.textView1);
      Button button = (Button) findViewById(R.id.boton);
      
      // Agregamos al textView un texto
      text.setText(R.string.cadena1);
      
      // Cambiamos el texto al botón
      button.setText(R.string.salir);
      
      // Evento onclick del botón, cuando se pulse, 
      // cerramos la actividad
      button.setOnClickListener(new OnClickListener() {
         public void onClick(View v) {
            finish();
         }
      });
   }
}
{% endhighlight %}

Para incluirlo en el documento, haremos lo siguiente:

{% highlight latex %}>\documentclass{article}

\usepackage{minted}

\newmintedfile[myJava]{java}{
    linenos,
    numbersep=5pt,
    gobble=0,
    frame=lines,
    framesep=2mm,
}

\begin{document}

Ejemplo de \textbackslash newmintedfile:
\myJava[label="miCodigo.java"]{miCodigo.java}
\end{document}
{% endhighlight %}

linenos muestra el número de línea, numbersep es la separación entre el código y el número de línea, gobble es la columna desde la que empezar a mostrar código, frame dibuja las líneas enmarcando el código y framsep es la separación entre la línea y el código.

El resultado será:  
<img src="http://elbauldelprogramador.com/content/uploads/2013/05/newmintedfileEjemplo.png" alt="newmintedfileEjemplo" width="733" height="940" class="thumbnail aligncenter size-full wp-image-1588" />

### Creando un comando

Puede resultar incómodo y pesado tener que escribir una y otra vez *\myJava[label=&#8221;\*&#8221;]{\*.java}*. Así que creamos un comando para facilitar las cosas:

{% highlight latex %}>\newmintedfile[myJava]{java}{
    linenos,
    numbersep=5pt,
    gobble=0,
    frame=lines,
    framesep=2mm,
}
\newcommand{\myJavaCode}[2]{
    \myJava[label=#2.java]{#1.java}
}
{% endhighlight %}

Ahora en lugar de usar *myJava* para incluir ficheros fuente en el documento, usamos un comando definido por nosotros (myJavaCode). Sustituyendo la línea *\myJava[label=&#8221;miCodigo.java&#8221;]{miCodigo.java}* del ejemplo anterior por

{% highlight latex %}>\myJavaCode{src/miCodigo}{miCodigo}
{% endhighlight %}

Obtenemos el mismo resultado, el primer argumento es la ruta al fichero y el segundo la etiqueta a mostrar en el documento.

### Conclusiones

Para mi, minted es el mejor paquete que hay para resaltar código en <img src="//s0.wp.com/latex.php?latex=%5CLaTeX&#038;bg=ffffff&#038;fg=000&#038;s=0" alt="&#92;LaTeX" title="&#92;LaTeX" class="latex" />. Y recomiendo a todo el mundo que aprenda a programar en <img src="//s0.wp.com/latex.php?latex=%5CLaTeX&#038;bg=ffffff&#038;fg=000&#038;s=0" alt="&#92;LaTeX" title="&#92;LaTeX" class="latex" />.

#### Referencias

*Manual de referencia Minted* **|** <a href="http://mirror.unl.edu/ctan/macros/latex/contrib/minted/minted.pdf" target="_blank">Descargar</a>  
*Repositorio del paquete* **|** <a href="http://code.google.com/p/minted/downloads/list" target="_blank">Visitar repositorio</a>

<div class="sharedaddy">
  <div class="sd-content">
    <ul>
      <li>
        <a class="hastip" rel="nofollow" href="http://twitter.com/home?status=Resaltar sintaxis del código fuente en LaTeX con minted+http://elbauldelprogramador.com/resaltar-sintaxis-del-codigo-fuente-en-latex-con-minted/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Twitter" target="_blank"><span class="iconbox-title"><i class="icon-twitter icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="http://www.facebook.com/sharer.php?u=http://elbauldelprogramador.com/resaltar-sintaxis-del-codigo-fuente-en-latex-con-minted/&t=Resaltar sintaxis del código fuente en LaTeX con minted+http://elbauldelprogramador.com/resaltar-sintaxis-del-codigo-fuente-en-latex-con-minted/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Facebook" target="_blank"><span class="iconbox-title"><i class="icon-facebook icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="https://plus.google.com/share?url=Resaltar sintaxis del código fuente en LaTeX con minted+http://elbauldelprogramador.com/resaltar-sintaxis-del-codigo-fuente-en-latex-con-minted/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en G+" target="_blank"><span class="iconbox-title"><i class="icon-google-plus icon-2x"></i></span></a>
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

 [1]: /opensource/disponible-la-primera-parte-del-curso/