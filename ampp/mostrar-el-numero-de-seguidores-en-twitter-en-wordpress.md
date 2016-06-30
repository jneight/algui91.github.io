---
title: Mostrar el número de seguidores en Twitter en WordPress

layout: post.amp

description: "Código php para mostrar en wordpress nuestro número de seguidores en twitter"
categories:
  - internet
tags:
  - contador seguidores
  - followers count
  - seguidores twitter
format: aside
main-class: "dev"
color: "#E64A19"
---
Hoy voy a explicar cómo mostrar los seguidores de nuestra cuenta de Twitter en el blog. Antes de nada, necesitamos instalar un plugin que nos permite añadir widgets que contengan código PHP, se llama WP PHP Widget y podéis descargarlo de la página de <a href="http://wordpress.org/extend/plugins/wp-php-widget/" target="_blank">plugins de WordPress</a>. Una vez que tengamos instalado el plugin, agregamos el widget a nuestra plantilla y pegamos el siguiente código:


<!--ad-->

```php
$url = "http://twitter.com/users/show/elbaulp";
$response = file_get_contents ( $url );
$t_profile = new SimpleXMLElement ( $response );
$count = $t_profile->followers_count;
echo $count;
```

Listo, esto imprimirá el número de seguidores que tenemos en twitter.

Este código no es mío, lo encontré navegando por internet, y le hice algunas modificaciones, concretamente creé una función que añadí al archivo `<strong>functions.php</strong>` del tema:

```php
function followers_count(){
  $url = "http://twitter.com/users/show/elbaulp";
  $response = file_get_contents ( $url );
  $t_profile = new SimpleXMLElement ( $response );
  $count = $t_profile->followers_count;
  return $count;
}
```

Y en el widget tengo lo siguiente:

```php
<p class="twittercount">
<a href="http://twitter.com/elbaulp" title="Follow on Twitter" target="_blank">
<amp-img layout="responsive" style="vertical-align:middle;padding-right:5px" src="twitter.png" alt="Twitter" /></a>
<span>< ?php echo followers_count();?></span> Followers

</p>

```

Y el estilo es el siguiente:

```css
.twittercount {
  -moz-box-shadow: 8px 11px 11px #222;
  -webkit-box-shadow: 8px 11px 11px #222;
  box-shadow: 8px 11px 11px #222;
  width: 127px;
  font-family: Georgia, serif;
  font-style: italic;
  text-shadow: 1px 1px 1px rgba(0, 0, 0, 0.5);
  text-align: center;
  border: 4px solid #EEE;
  border-radius: 5px;
  background-color: "#EEE";
  margin: 5px;
  display: inline-block;
}

```

<div style="text-align:center">
<p class="twittercount">
<a href="http://twitter.com/elbaulp" title="Follow on Twitter" target="_blank"><amp-img layout="responsive" style="vertical-align:middle;padding-right:5px" src="/wp-content/themes/ifeature/assets/img/social/round/twitter.png" alt="Twitter" /></a><span>693</span> Followers
  </p>
</div>

Eso es todo.



{% include toc.html %}