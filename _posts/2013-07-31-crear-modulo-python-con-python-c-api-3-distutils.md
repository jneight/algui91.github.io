---
id: 1788
title: 'Crear un módulo para python con la Python C API (III) &#8211; DistUtils'
author: Alejandro Alcalde
layout: post
guid: http://elbauldelprogramador.com/?p=1788
permalink: /crear-modulo-python-con-python-c-api-3-distutils/
categories:
  - C
  - python
tags:
  - embebiendo python en c++
  - reference count python
  - tutorial crear modulos python
  - tutorial python c api
---
  * [Crear un módulo para python con la Python C API (I) – Introducción][1]
  * [Crear un módulo para python con la Python C API (II) – Primer ejemplo][2]
  * Crear un módulo para python con la Python C API (III) – DistUtils
  * [Crear un módulo para python con la Python C API (IV) – HerramientasRed][3]
  * [Crear un módulo para python con la Python C API (V) – Python 3][4]

<img class="thumbnail alignleft size-full wp-image-1777" alt="Crear un módulo para python con la Python C API - DistUtils" src="http://elbauldelprogramador.com/content/uploads/2013/03/Crear-un-módulo-para-python-con-la-Python-C-API-Parte-I.png" width="201" height="190" />  
Como dijimos en la entrada[ anterior][5], vamos a hablar de *DistUtils*, una herramienta con la que seremos capaces de automatizar el proceso de compilación e instalación de nuestro módulo creado con la Python C API.

  
<!--more-->

### Compilar el módulo

El esquema básico de un paquete **distutils** contiene un fichero *setup.py*, su versión más simple es:

{% highlight python %}>from distutils.core import setup, Extension

module1 = Extension('ejemplo',
                    sources = ['ejemplo.c'])

setup (name = 'NombreDelPaquete',
       version = '1.0',
       description = 'Descripción del paquete',
       ext_modules = [module1]){% endhighlight %}

Una vez definido el *setup.py* ejecutando

{% highlight bash %}>$ python setup.py build
running build
running build_ext
building 'ejemplo' extension
x86_64-linux-gnu-gcc -pthread -fno-strict-aliasing -DNDEBUG -g -fwrapv -O2 -Wall -Wstrict-prototypes -fPIC -I/usr/include/python2.7 -c ejemplo.c -o build/temp.linux-x86_64-2.7/ejemplo.o
creating build/lib.linux-x86_64-2.7
x86_64-linux-gnu-gcc -pthread -shared -Wl,-O1 -Wl,-Bsymbolic-functions -Wl,-z,relro -fno-strict-aliasing -DNDEBUG -g -fwrapv -O2 -Wall -Wstrict-prototypes -D_FORTIFY_SOURCE=2 -g -fstack-protector --param=ssp-buffer-size=4 -Wformat -Werror=format-security build/temp.linux-x86_64-2.7/ejemplo.o -o build/lib.linux-x86_64-2.7/ejemplo.so

$ ll build/lib.linux-x86_64-2.7/ejemplo.so
-rwxr-xr-x 1 hkr hkr 18K Jul 26 16:57 build/lib.linux-x86_64-2.7/ejemplo.so*{% endhighlight %}

compilaremos el fichero *ejemplo.c* y se generará un módulo llamado *ejemplo* en el directorio *build*.

Normalmente los módulos son más complejos y es necesario usar librerías externas, indicar dónde se encuentran las cabeceras etc, para ello se usan estas reglas:

{% highlight python %}>from distutils.core import setup, Extension

module1 = Extension('demo',
                    define_macros = [('MAJOR_VERSION', '1'),
                                     ('MINOR_VERSION', '0')],
                    include_dirs = ['/usr/local/include'],
                    libraries = ['tcl83'],
                    library_dirs = ['/usr/local/lib'],
                    sources = ['demo.c'])

setup (name = 'PackageName',
       version = '1.0',
       description = 'This is a demo package',
       author = 'Martin v. Loewis',
       author_email = 'martin@v.loewis.de',
       url = 'http://docs.python.org/extending/building',
       long_description = '''
This is really just a demo package.
''',
       ext_modules = [module1]){% endhighlight %}

### Instalar el módulo

Sin embargo, con *python setup.py build* no se está instalando el módulo en el lugar adecuado para que python sea capaz de utilizarlo, para ello es necesario usar *install* de a siguiente forma:

{% highlight bash %}># python setup.py install
running install
running build
running build_ext
building 'ejemplo' extension
creating build
creating build/temp.linux-x86_64-2.7
x86_64-linux-gnu-gcc -pthread -fno-strict-aliasing -DNDEBUG -g -fwrapv -O2 -Wall -Wstrict-prototypes -fPIC -I/usr/include/python2.7 -c ejemplo.c -o build/temp.linux-x86_64-2.7/ejemplo.o
creating build/lib.linux-x86_64-2.7
x86_64-linux-gnu-gcc -pthread -shared -Wl,-O1 -Wl,-Bsymbolic-functions -Wl,-z,relro -fno-strict-aliasing -DNDEBUG -g -fwrapv -O2 -Wall -Wstrict-prototypes -D_FORTIFY_SOURCE=2 -g -fstack-protector --param=ssp-buffer-size=4 -Wformat -Werror=format-security build/temp.linux-x86_64-2.7/ejemplo.o -o build/lib.linux-x86_64-2.7/ejemplo.so
running install_lib
copying build/lib.linux-x86_64-2.7/ejemplo.so -> /usr/local/lib/python2.7/dist-packages
running install_egg_info
Removing /usr/local/lib/python2.7/dist-packages/NombreDelPaquete-1.0.egg-info
Writing /usr/local/lib/python2.7/dist-packages/NombreDelPaquete-1.0.egg-info{% endhighlight %}

Y ahora sí que podremos usar el módulo como antes:

{% highlight python %}>In [1]: import ejemplo

In [2]: ejemplo.saluda('Alejandro')
Out[2]: 'Hola Alejandro desde la Python C API!'{% endhighlight %}

En la siguiente parte, crearemos un módulo que sea capaz de devolver la ip de un dominio web.

#### Referencias

*Documentación de Distutils* **|** <a href="http://docs.python.org/3/extending/building.html#building" target="_blank">docs.python</a> 

<div class="sharedaddy">
  <div class="sd-content">
    <ul>
      <li>
        <a class="hastip" rel="nofollow" href="http://twitter.com/home?status=Crear un módulo para python con la Python C API (III) &#8211; DistUtils+http://elbauldelprogramador.com/crear-modulo-python-con-python-c-api-3-distutils/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Twitter" target="_blank"><span class="iconbox-title"><i class="icon-twitter icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="http://www.facebook.com/sharer.php?u=http://elbauldelprogramador.com/crear-modulo-python-con-python-c-api-3-distutils/&t=Crear un módulo para python con la Python C API (III) &#8211; DistUtils+http://elbauldelprogramador.com/crear-modulo-python-con-python-c-api-3-distutils/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Facebook" target="_blank"><span class="iconbox-title"><i class="icon-facebook icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="https://plus.google.com/share?url=Crear un módulo para python con la Python C API (III) &#8211; DistUtils+http://elbauldelprogramador.com/crear-modulo-python-con-python-c-api-3-distutils/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en G+" target="_blank"><span class="iconbox-title"><i class="icon-google-plus icon-2x"></i></span></a>
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

 [1]: http://elbauldelprogramador.com/crear-modulo-python-con-python-c-api-1/ "Crear un módulo para python con la Python C API (I)"
 [2]: http://elbauldelprogramador.com/crear-modulo-python-con-python-c-api-2/ "Crear un módulo para python con la Python C API (II)"
 [3]: http://elbauldelprogramador.com/crear-modulo-python-con-python-c-api-4/ "Crear un módulo para python con la Python C API (IV)"
 [4]: http://elbauldelprogramador.com/crear-modulo-python-con-python-c-api-5-python3/ "Crear un módulo para python con la Python C API (V)"
 [5]: http://elbauldelprogramador.com/python/crear-modulo-python-con-python-c-api-2