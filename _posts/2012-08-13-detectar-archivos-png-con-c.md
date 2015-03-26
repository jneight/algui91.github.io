---
id: 847
title: Detectar archivos PNG con C++
author: Alejandro Alcalde
layout: post
guid: /?p=847
permalink: /detectar-archivos-png-con-c/
if_slider_image:
  - 
  - 
categories:
  - C
tags:
  - imgen png
  - leer formato archivos png
  - leer imagen png c++
  - ocultar imagenes en imagenes
---
Hace un tiempo trasteando con una librería que nos proporcionaron en la facultad (desarrollada por J.Baena y A. Garrido para las asignaturas de introducción a la programación en la ETSIIT [Escuela Técnica Superior de Ingenierías Informática y de Telecomunicación en Granada] de la UGR, Universidad de Granada.) para leer imágenes PGM y PPM, me propuse añadirle la capacidad de leer, o al menos detectar imágenes PNG, teniendo el permiso de ambos autores para publicar parte de la librería.

Por supuesto existen librerías que ya hacen este trabajo, como <a href="http://www.libpng.org/pub/png/libpng.html" target="_blank">libpng</a>, pero quise intentarlo con fines didácticos. Y lo comparto con vosotros.

El código solo detecta si la imagen es PNG, no implementé la lectura de la imagen en sí ya que hay que tener muchos factores en cuenta (transparencia, color de fondo, corrección Gamma, histograma etc).

Veamos primero el .h:  
  
<!--more-->

{% highlight cpp %} >/**
  * @file imagenES.h
  * @brief Fichero cabecera para la E/S de imágenes
  *
  * Permite la E/S de archivos de tipos PGM,PPM
  *
  */

#ifndef _IMAGEN_ESP_H_
#define _IMAGEN_ESP_H_
/**
  * @brief Tipo de imagen
  *
  * Declara una serie de constantes para representar los distintos tipos
  * de imágenes que se pueden manejar.
  *
  * @see LeerTipoImagen
  */
enum TipoImagen {IMG_DESCONOCIDO, ///&lt; Tipo de imagen desconocido
                 IMG_PGM,         ///&lt; Imagen tipo PGM
                 IMG_PPM,         ///&lt; Imagen tipo PPM
                 IMG_PNG         ///&lt; Imagen PNG
               };


/**
  * @brief Consulta el tipo de imagen del archivo y sus dimensiones
  *
  * @param nombre indica el nombre del archivo de disco a consultar
  * @param filas Parámetro de salida con las filas de la imagen.
  * @param columnas Parámetro de salida con las columnas de la imagen.
  * @return Devuelve el tipo de la imagen en el archivo
  *
  * @see TipoImagen
  */
TipoImagen LeerTipoImagen(const char nombre[], int&#038; filas, int&#038; columnas);

/**
  * @brief Lee una imagen de tipo PNG sobre memoria reservada
  *
  * @param nombre nombre del archivo a leer
  * @param filas Parámetro de salida con las filas de la imagen.
  * @param columnas Parámetro de salida con las columnas de la imagen. 
  * @return si ha  tenido éxito en la lectura
  */
bool LeerImagenPNG (const char nombre[], int&#038; filas, int&#038; columnas);

/**
  * @brief Lee una imagen de tipo PPM sobre memoria reservada
  *
  * @param nombre nombre del archivo a leer
  * @param filas Parámetro de salida con las filas de la imagen.
  * @param columnas Parámetro de salida con las columnas de la imagen.
  * @param buffer Zona de memoria para obtener el valor de cada uno de los píxeles
  * como tripletas consecutivas en formato RGB (RGBRGBRGB...) por filas
  * desde la esquina superior izqda a la inferior drcha. 
  * @return si ha  tenido éxito en la lectura
  * @pre buffer debe ser una zona de memoria suficientemente grande como para
  * almacenar @a filas x @a columnas x 3  * bytes de datos de la imagen.
  */
bool LeerImagenPPM (const char nombre[], int&#038; filas, int&#038; columnas, unsigned char buffer[]);

/**
  * @brief Escribe una imagen de tipo PPM
  *
  * @param nombre nombre del archivo a escribir
  * @param datos punteros a los @a f x @a c x 3 bytes que corresponden a los valores
  *    de los píxeles de la imagen en formato RGB.
  * @param f filas de la imagen
  * @param c columnas de la imagen
  * @retval true si ha tenido éxito en la escritura.
  * @retval false si se ha producido algún error en la escritura.
  */
bool EscribirImagenPPM (const char nombre[], const unsigned char datos[], int f, int c);

/**
  * @brief Lee una imagen de tipo PGM sobre memoria reservada
  *
  * @param nombre nombre del archivo a leer
  * @param filas Parámetro de salida con las filas de la imagen.
  * @param columnas Parámetro de salida con las columnas de la imagen.
  * @param buffer Zona de memoria para obtener el valor de cada uno de los píxeles
  * como un valor de gris desde la esquina superior izqda a la inferior drcha. 
  * @return si ha  tenido éxito en la lectura
  * @pre buffer debe ser una zona de memoria suficientemente grande como para
  * almacenar @a filas x @a columnas bytes de datos de la imagen.
  */
bool LeerImagenPGM (const char nombre[], int&#038; filas, int&#038; columnas, unsigned char buffer[]);

/**
  * @brief Escribe una imagen de tipo PGM
  *
  * @param nombre nombre del archivo a escribir
  * @param datos punteros a los @a f x @a c bytes que corresponden a los valores
  *    de los píxeles de la imagen de grises.
  * @param f filas de la imagen
  * @param c columnas de la imagen
  * @retval true si ha tenido éxito en la escritura.
  * @retval false si se ha producido algún error en la escritura.
  */
bool EscribirImagenPGM (const char nombre[], const unsigned char datos[], int f, int c);

#endif

/* Fin Fichero: imagenES.h */
{% endhighlight %}

Y el cpp:

{% highlight cpp %} >/**
  * @file imagenES.cpp
  * @brief Fichero con definiciones para la E/S de imágenes
  *
  * Permite la E/S de archivos de tipo PGM,PPM
  *
  */

#include &lt;fstream>
#include &lt;string>
#include &lt;iostream>
#include &lt;netinet/in.h>

#include "./imagenESP.h"

using namespace std;


TipoImagen LeerTipo(ifstream&#038; f)
{
  char c1,c2;
  TipoImagen res= IMG_DESCONOCIDO;

  if (f) {
    
    /*
        The first eight bytes of a PNG file always contain the following values:
        (decimal)              137  80  78  71  13  10  26  10
        (hexadecimal)           89  50  4e  47  0d  0a  1a  0a
        (ASCII C notation)    211   P   N   G  r  n �32 n
    */
    unsigned char cas[8];
    unsigned char check[8] = {0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A};
    bool fallo = false;
    f.read(reinterpret_cast&lt;char*>(cas), 8);
    for (int i = 0; i &lt; 8 &#038;&#038; !fallo; i++)
        if (check[i] != cas[i])
            fallo = true;
            //cout.setf ( ios::hex, ios::basefield );
            //cout.setf ( ios::showbase ); 
            //cout &lt;&lt; (int)check[i] &lt;&lt; " "; 
            
    if (!fallo)
        return IMG_PNG;
  
    f.seekg(0L, ios::beg);
    
    c1=f.get();
    c2=f.get();

    if (f &#038;&#038; c1=='P')
      switch (c2) {
        case '5': res= IMG_PGM; break;
        case '6': res= IMG_PPM; break;
        default: res= IMG_DESCONOCIDO;
      }
  }
  return res;
}

// _____________________________________________________________________________

char SaltarSeparadores (ifstream&#038; f)
{
  char c;
  do {
    c= f.get();
  } while (isspace(c));
  f.putback(c);
  return c;
}

// _____________________________________________________________________________

bool LeerCabecera (ifstream&#038; f, int&#038; filas, int&#038; columnas, bool png = false)
{
    //Si la imagen es PNG leemos algunos datos.
 if (png){
       f.seekg(16L, ios::beg);

     f.read(reinterpret_cast&lt;char*>(&#038;columnas), 4);
      f.read(reinterpret_cast&lt;char*>(&#038;filas), 4);

     columnas = ntohl(columnas);
     filas = ntohl(filas);

       return true;
    }else{
      int maxvalor;

       while (SaltarSeparadores(f)=='#')
         f.ignore(10000,'n');

          f >> columnas >> filas >> maxvalor;

         if (/*str &#038;&#038;*/ f &#038;&#038; filas>0 &#038;&#038; filas &lt;5000 &#038;&#038; columnas >0 &#038;&#038; columnas&lt;5000) {
               f.get(); // Saltamos separador
              return true;
            }
           else return false;
  }
}

// _____________________________________________________________________________

TipoImagen LeerTipoImagen(const char nombre[], int&#038; filas, int&#038; columnas)
{
  TipoImagen tipo;
  filas=columnas=0;
  ifstream f(nombre);

  tipo=LeerTipo(f);
  if (tipo!=IMG_DESCONOCIDO)
    if (!LeerCabecera(f,filas,columnas, tipo == IMG_PNG)) {
      tipo=IMG_DESCONOCIDO;
    }

  return tipo;
}


// _____________________________________________________________________________

bool LeerImagenPNG (const char nombre[], int&#038; filas, int&#038; columnas){

 bool exito = false;
 filas      = 0;
 columnas   = 0;
 ifstream f(nombre);

 if (LeerTipo(f) == IMG_PNG)
     if (LeerCabecera(f, filas, columnas, true))
         exito = true;
           //if (f.read(reinterpret_cast&lt;char *>(buffer), filas * columnas))

   return exito;
}

/* Fin Fichero: imagenES.cpp */
       //if (f.read(reinterpret_cast&lt;char *>(buffer), filas * columnas))

   return exito;
}

/* Fin Fichero: imagenES.cpp */

{% endhighlight %}

Podemos usar *TipoImagen LeerTipoImagen(const char nombre[], int& filas, int& columnas)*, ella se encargará de llamar a las demás en el orden adecuado. Expliquemos el flujo de ejecución tras llamarla:

Esta función recibe como primer parámetro la ruta de la imagen, y se pasan por referencia dos variables llamadas filas y columnas donde se almacenará la dimensión de la imagen. Dentro de **LeerTipoImagen** se llama a **LeerTipo**.

Todos las imágenes PNG tienen una cabecera de 8 bytes que las identifica, en hexadecimal corresponden a los valores *89 50 4e 47 0d 0a 1a 0a*. **LeerTipo** lee los 8 primeros bytes y los compara con éstos valores, si coinciden es una imagen PNG, el resto de la función detecta imágenes PPM y PGM.

Una vez detectada la imagen, se llama a **LeerCabecera (ifstream& f, int& filas, int& columnas, bool png = false)**, que se posiciona en el byte 16 de la imágen y lee 4 bytes correspondientes al ancho de la imagen y otros 4 para el alto. Internamente están almacenados en orden netlong, los convertimos a enteros de orden hostlong con **<a href="http://beej.us/guide/bgnet/output/html/multipage/htonsman.html" target="_blank">ntohl()</a>**

En lugar de usar la función *TipoImagen LeerTipoImagen(const char nombre[], int& filas, int& columnas)* se puede usar también *bool LeerImagenPNG (const char nombre[], int& filas, int& columnas* veamos un ejemplo completo:

{% highlight cpp %} >/**
 * @file main.cpp
 * @brief Fichero principal encargado de ocultar el mensaje
 *
 *  Created on: Aug 12, 2012
 *      @author: Alejandro Alcalde
 */
#include "./imagenESP.h"

#include &lt;iostream>

using namespace std;

int main(int argc, char *argv[]) {

    int filas;
    int columnas;

    cout &lt;&lt; "Probando con LeerTipoImagen: ";
   TipoImagen tipo = LeerTipoImagen(argv[1], filas, columnas);
 cout &lt;&lt; "INFO: La imagen tiene " &lt;&lt; columnas &lt;&lt; " pixels de ancho y " &lt;&lt; filas
       &lt;&lt; " pixels de alto.n";
    cout &lt;&lt; "Tipo imagen: " &lt;&lt; tipo &lt;&lt; endl;
    
    cout &lt;&lt; "Probando con LeerImagenPNG: ";
 LeerImagenPNG(argv[1], filas, columnas);
    cout &lt;&lt; "INFO: La imagen tiene " &lt;&lt; columnas &lt;&lt; " pixels de ancho y " &lt;&lt; filas
       &lt;&lt; " pixels de alto.n";
    cout &lt;&lt; "Tipo imagen: " &lt;&lt; tipo &lt;&lt; endl;
    return 0;
}
{% endhighlight %}

Cuya salida es:

{% highlight bash %} >hkr-> ls
imagenESP.cpp  imagenESP.h  main  main.cpp  Makefile  s.png
[~/Documents/Grado Ing.Informatica/MP/Practica 3/mp_grado/PNG]
hkr-> g++ -Wall -I./ *.cpp -o main
[~/Documents/Grado Ing.Informatica/MP/Practica 3/mp_grado/PNG]
hkr-> ./main s.png
Probando con LeerTipoImagen: INFO: La imagen tiene 1600 pixels de ancho y 1000 pixels de alto.
Tipo imagen: 3
Probando con LeerImagenPNG: INFO: La imagen tiene 1600 pixels de ancho y 1000 pixels de alto.
Tipo imagen: 3

{% endhighlight %}

* * *Información PNG | 

<a href="http://www.libpng.org/pub/png/spec/1.1/PNG-Rationale.html#R.PNG-file-signature" target="_blank">libpng</a>  
Documentación | <a href="http://www.fileformat.info/format/png/corion.htm" target="_blank">fileformat.info</a>  
Leer tamaño imagen | <a href="http://stackoverflow.com/questions/5354459/c-how-to-get-the-image-size-of-a-png-file-in-directory" target="_blank">StackOverflow</a></p> 

<div class="sharedaddy">
  <div class="sd-content">
    <ul>
      <li>
        <a class="hastip" rel="nofollow" href="http://twitter.com/home?status=Detectar archivos PNG con C+++http://elbauldelprogramador.com/detectar-archivos-png-con-c/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Twitter" target="_blank"><span class="iconbox-title"><i class="icon-twitter icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="http://www.facebook.com/sharer.php?u=http://elbauldelprogramador.com/detectar-archivos-png-con-c/&t=Detectar archivos PNG con C+++http://elbauldelprogramador.com/detectar-archivos-png-con-c/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Facebook" target="_blank"><span class="iconbox-title"><i class="icon-facebook icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="https://plus.google.com/share?url=Detectar archivos PNG con C+++http://elbauldelprogramador.com/detectar-archivos-png-con-c/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en G+" target="_blank"><span class="iconbox-title"><i class="icon-google-plus icon-2x"></i></span></a>
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