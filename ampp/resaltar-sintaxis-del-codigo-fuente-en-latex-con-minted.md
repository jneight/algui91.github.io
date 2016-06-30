---
title: Resaltar sintaxis del código fuente en LaTeX con minted
modified: 2015-04-08T00:00:00+00:00
math: true
layout: post.amp

categories:
  - LaTeX
tags:
  - ejemplos minted
  - instalar minted
  - pygments
  - resaltar sintaxis codigo latex
  - latex
image: 2013/05/latex_logo.png
main-class: "latex"
color: "#B31917"
---

Hace unas semanas que aprendí a usar $$\LaTeX$$, y cada vez me gusta más, proporciona una calidad a los documentos impecable. De hecho, estoy entregando las prácticas de la facultad en <amp-img layout="responsive" src="//s0.wp.com/latex.php?latex=%5CLaTeX&bg=ffffff&fg=000&s=0" alt="\LaTeX" title="\LaTeX" class="latex" /> y he reescrito el [Curso de programación Android][1] por completo.

Sin embargo, una de las cosas que más me ha costado conseguir es encontrar alguna forma que me gustase de resaltar la sintaxis en latex del código fuente. Tras mucho buscar por internet encontré un paquete que concluyó con mi búsqueda, se llama **minted**.  

<!--ad-->

### Instalando dependencias

Para instalarlo, es necesaria una versión de python igual o superior a la 2.6, y *Pygments*. Para instalar el último ejecuta:

```bash
# easy_install Pygments

```

Si no tienes instalado el programa *easy_install*, ejecuta:

```bash
# aptitude install python-setuptools

```

### Instalar minted

Descarga el paquete desde su <a href="http://code.google.com/p/minted/downloads/list" target="_blank">repositorio</a>. Extráelo y sitúate en su directorio. Luego ejecuta la instrucción *make*.

### Algunos ejemplos

Ya está todo listo para usar, empecemos con un ejemplo básico extraido del manual, disponible para descargar en las referencias:

```latex
\documentclass{article}

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

```

Este trozo de código dará como resultado lo siguiente:  
<amp-img layout="responsive" src="/assets/img/2013/05/mintedEjemploC.png" alt="Ejemplo minted C" width="599px" height="246px" />

### Insertar código desde un archivo de código fuente

Normalmente, si tenemos un código fuente con muchas líneas es más cómodo incluirlo directamente en <amp-img layout="responsive" src="//s0.wp.com/latex.php?latex=%5CLaTeX&bg=ffffff&fg=000&s=0" alt="\LaTeX" title="\LaTeX" class="latex" /> en lugar de copiar todas esas líneas. **Minted** proporciona un comando para tal fin. *\newmintedfile[]{}*. Veamos un ejemplo:

```latex
\newmintedfile[myJava]{java}{
    linenos,
    numbersep=5pt,
    gobble=0,
    frame=lines,
    framesep=2mm,
}

```

Con este comando, hemos definido una nueva función (*myJava*), que permitirá incluir el código fuente de un archivo al documento pdf. Por ejemplo. Supongamos que el contenido del fichero *miCodigo.java* es el siguiente:

```java
package com.elbauldelprogramador.actividades;

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

```

Para incluirlo en el documento, haremos lo siguiente:

```latex
\documentclass{article}

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

```

linenos muestra el número de línea, numbersep es la separación entre el código y el número de línea, gobble es la columna desde la que empezar a mostrar código, frame dibuja las líneas enmarcando el código y framsep es la separación entre la línea y el código.

El resultado será:  
<amp-img layout="responsive" src="/assets/img/2013/05/newmintedfileEjemplo.png" alt="newmintedfileEjemplo" width="733px" height="940px" />

### Creando un comando

Puede resultar incómodo y pesado tener que escribir una y otra vez *\myJava[label=&#8221;\*&#8221;]{\*.java}*. Así que creamos un comando para facilitar las cosas:

```latex
\newmintedfile[myJava]{java}{
    linenos,
    numbersep=5pt,
    gobble=0,
    frame=lines,
    framesep=2mm,
}
\newcommand{\myJavaCode}[2]{
    \myJava[label=#2.java]{#1.java}
}

```

Ahora en lugar de usar *myJava* para incluir ficheros fuente en el documento, usamos un comando definido por nosotros (myJavaCode). Sustituyendo la línea *\myJava[label=&#8221;miCodigo.java&#8221;]{miCodigo.java}* del ejemplo anterior por

```latex
\myJavaCode{src/miCodigo}{miCodigo}

```

Obtenemos el mismo resultado, el primer argumento es la ruta al fichero y el segundo la etiqueta a mostrar en el documento.

### Conclusiones

Para mi, minted es el mejor paquete que hay para resaltar código en <amp-img layout="responsive" src="//s0.wp.com/latex.php?latex=%5CLaTeX&bg=ffffff&fg=000&s=0" alt="\LaTeX" title="\LaTeX" class="latex" />. Y recomiendo a todo el mundo que aprenda a programar en <amp-img layout="responsive" src="//s0.wp.com/latex.php?latex=%5CLaTeX&bg=ffffff&fg=000&s=0" alt="\LaTeX" title="\LaTeX" class="latex" />.

#### Referencias

*Manual de referencia Minted* »» <a href="http://mirror.unl.edu/ctan/macros/latex/contrib/minted/minted.pdf" target="_blank">Descargar</a>  
*Repositorio del paquete* »» <a href="http://code.google.com/p/minted/downloads/list" target="_blank">Visitar repositorio</a>



 [1]: /disponible-la-primera-parte-del-curso/

{% include toc.html %}