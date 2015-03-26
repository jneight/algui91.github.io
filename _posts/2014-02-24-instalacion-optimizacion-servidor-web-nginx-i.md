---
id: 2253
title: Instalación y optimización de un servidor web con Nginx (I)
author: Alejandro Alcalde
layout: post
guid: http://elbauldelprogramador.com/?p=2253
permalink: /instalacion-optimizacion-servidor-web-nginx-i/
categories:
  - Administración de Servidores
  - linux
tags:
  - configuracion nginx
  - instalar nginx linux
  - montar un servidor web
---
> La siguiente serie de artículos son el fruto de un trabajo realizado para la facultad en la asignatura Ingeniería de Servidores de la Universidad de Granada (ETSIIT [Escuela Técnica Superior de Ingenierías Informática y de Telecomunicación] )

*A lo largo de esta guía se pretende mostrar cómo instalar desde cero un servidor web con Nginx, realizando las operaciones necesarias para lograr el mayor rendimiento y seguridad posibles con programas tales como php-fpm, APC, y el módulo pagespeed de Google para optimizar los recursos web.  
*

# Tabla de contenidos

  * Instalación y optimización de un servidor web con Nginx (I)
  * [Instalación y optimización de un servidor web con Nginx (II)][1]
  * [Instalación y optimización de un servidor web con Nginx (III)][2]

Hace tiempo se vío en este blog [cómo instalar y configurar Nginx con php5-fpm][3], en los próximos artículos se intentará explicar de forma más detallada cómo llevar a cabo éste proceso junto con algunas mejoras adicionales en términos de optimización. Es necesario informar al lector que todas las recomendaciones aquí explicadas se basan únicamente en la experiencias del autor.

<!--more-->

## Compilar e instalar Nginx

### Preparando el entorno

Lo primero que debemos hacer es instalar las dependencias necesarias para la compilación, para ello:

{% highlight bash %}>apt-get install build-essential libssl-dev libpcre3-dev
{% endhighlight %}

Tras esto descargamos la última versión de nginx, al momento de escribir este texto la 1.4.4:

{% highlight bash %}>wget http://nginx.org/download/nginx-1.4.4.tar.gz
{% endhighlight %}

Descomprimimos el archivo:

{% highlight bash %}>tar xzvf nginx-1.4.4.tar.gz
{% endhighlight %}

Antes de compilar cambiaremos un valor en el código fuente como medida de seguridad por ocultación. El valor a cambiar es la cadena asignada a la cabecera que indica el servidor usado en las peticiones HTTP. En concreto el archivo a cambiar es el alojado en `src/http/ngx_http_header_filter_module.c`, concretamente en la línea 48:

{% highlight c %}>static char ngx_http_server_string[] = "Server: nginx" CRLF;
static char ngx_http_server_full_string[] = "Server: " NGINX_VER CRLF;
{% endhighlight %}

Cambiamos estas dos líneas a algo del estilo:

{% highlight c %}>static char ngx_http_server_string[] = "Server: Mi servidor Web" CRLF;
static char ngx_http_server_full_string[] = "Server: Mi servidor Web" CRLF;
{% endhighlight %}

Ya solo queda compilarlo e instalarlo, de momento necesitaremos los módulos siguientes:

{% highlight bash %}>--with-http_gzip_static_module --sbin-path=/usr/local/sbin -with-http_ssl_module --without-mail_pop3_module --without-mail_imap_module --without-mail_smtp_module --with-http_stub_status_module --with-http_realip_module
{% endhighlight %}

Aquí estamos habilitando la compresión de las páginas con gzip, SSL para conexiones seguras, deshabilitando el módulo de correo POP3, IMAP y SMTP. Dependiendo de las necesidades de nuestro servidor, deberemos activar o desactivar algunos módulos. Más tarde necesitaremos recompilar para añadir el módulo **pagespeed**. La lista de todos los módulos disponibles se puede consultar en la <a href="http://wiki.nginx.org/Modules" title="Módulos nginx" target="_blank">documentación de nginx</a>.

### Compilar

Ya está todo listo para compilar e instalar, dentro del directorio de nginx ejecutamos:

{% highlight bash %}>./configure --with-http_gzip_static_module --sbin-path=/usr/local/sbin \
--with-http_ssl_module --without-mail_pop3_module --without-mail_imap_module\
--without-mail_smtp_module --with-http_stub_status_module --with-http_realip_module
{% endhighlight %}

Tras esto deberíamos ver un resumen de la operación realizada:

{% highlight bash %}>Configuration summary
  + using system PCRE library
  + using system OpenSSL library
  + md5: using OpenSSL library
  + sha1: using OpenSSL library
  + using system zlib library

  nginx path prefix: "/usr/local/nginx"
  nginx binary file: "/usr/local/sbin"
  nginx configuration prefix: "/usr/local/nginx/conf"
  nginx configuration file: "/usr/local/nginx/conf/nginx.conf"
  nginx pid file: "/usr/local/nginx/logs/nginx.pid"
  nginx error log file: "/usr/local/nginx/logs/error.log"
  nginx http access log file: "/usr/local/nginx/logs/access.log"
  nginx http client request body temporary files: "client_body_temp"
  nginx http proxy temporary files: "proxy_temp"
  nginx http fastcgi temporary files: "fastcgi_temp"
  nginx http uwsgi temporary files: "uwsgi_temp"
  nginx http scgi temporary files: "scgi_temp"
{% endhighlight %}

Para compilar e instalar:

{% highlight bash %}>make -j 4 &#038;&#038; make install
{% endhighlight %}

Tras esto, es necesario descargar el script que permite iniciar, detener, reiniciar y recargar nginx mediante el comando **service**, podemos descargarlo desde

{% highlight bash %}>wget https://raw.github.com/JasonGiedymin/nginx-init-ubuntu/master/nginx
mv nginx /etc/init.d/nginx
sudo chmod +x /etc/init.d/nginx
sudo chown root:root /etc/init.d/nginx
update-rc.d nginx defaults
{% endhighlight %}

Con esto hemos descargaro el script, lo hemos movido al directorio en el que será llamado al inicio del sistema, dado permisos de ejecución y asignado a root como propietario. Hecho esto, para iniciar nuestro servidor web hay que ejecutar el comando:

{% highlight bash %}>service nginx start
{% endhighlight %}

Como se muestra en la siguiente figura nginx, podemos comprobar que nginx está funcionando correctamente dirigiéndonos a la dirección *localhost*, donde veremos lo siguiente:

<img src="http://elbauldelprogramador.com/content/uploads/2014/02/instalacionNginx.png" alt="Instalación y optimización de un servidor web con Nginx (I)" width="554" height="192" class="aligncenter size-full wp-image-2254" />

### Configuración

Ya que está todo listo, vamos a realizar unos cuantos ajustes a la configuración por defecto:

{% highlight bash %}>user  www-data;
worker_processes  1;

pid        /var/run/nginx.pid;

error_log  logs/error.log;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    gzip on;
    gzip_buffers 16 8k;
    gzip_disable "MSIE [1-6]\.";
    gzip_proxied any;
    gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  logs/access.log  main;

    sendfile        on;
    keepalive_timeout  3;
    index              index.html index.htm;

    server {
        listen       80;
        server_name localhost;
        root html;

    access_log  logs/host.access.log  main;

        # Deny all attempts to access hidden files such as .htaccess, .htpasswd, .DS_Store (Mac).
        location ~ /\. {
                deny all;
                access_log off;
                log_not_found off;
        }

    }

}
{% endhighlight %}

Los cambios más relevantes sobre la configuración por defecto son:

  * Se ha cambiado el usuario del servidor de *nobody* a *www-data*, éste último es el usuario por defecto para servidores webs.
  * Se define el archivo donde se localizará el PID (Process ID) del servidor. Esto permite al script que hemos instalado iniciar o detener nginx.
  * Se habilita la compresión gzip para reducir el ancho de banda consumido.
  * Se define el formato que tendrán los ficheros de log.

Cambiamos los permisos del directorio donde se alojan los recursos web a este último usuario y reiniciamos nginx:

{% highlight bash %}>chown -R www-data:www-data /usr/local/nginx/html/
service nginx destroy &#038;&#038; service nginx start
{% endhighlight %}

<div class="sharedaddy">
  <div class="sd-content">
    <ul>
      <li>
        <a class="hastip" rel="nofollow" href="http://twitter.com/home?status=Instalación y optimización de un servidor web con Nginx (I)+http://elbauldelprogramador.com/instalacion-optimizacion-servidor-web-nginx-i/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Twitter" target="_blank"><span class="iconbox-title"><i class="icon-twitter icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="http://www.facebook.com/sharer.php?u=http://elbauldelprogramador.com/instalacion-optimizacion-servidor-web-nginx-i/&t=Instalación y optimización de un servidor web con Nginx (I)+http://elbauldelprogramador.com/instalacion-optimizacion-servidor-web-nginx-i/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Facebook" target="_blank"><span class="iconbox-title"><i class="icon-facebook icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="https://plus.google.com/share?url=Instalación y optimización de un servidor web con Nginx (I)+http://elbauldelprogramador.com/instalacion-optimizacion-servidor-web-nginx-i/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en G+" target="_blank"><span class="iconbox-title"><i class="icon-google-plus icon-2x"></i></span></a>
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

 [1]: http://elbauldelprogramador.com/instalacion-optimizacion-servidor-web-nginx-ii "Instalación y optimización de un servidor web con Nginx (II)"
 [2]: http://elbauldelprogramador.com/instalacion-optimizacion-servidor-web-nginx-iii "Instalación y optimización de un servidor web con Nginx (III)"
 [3]: http://elbauldelprogramador.com/como-instalar-nginx-con-php5-fpm/ "Cómo instalar y configurar Nginx con php5-fpm"