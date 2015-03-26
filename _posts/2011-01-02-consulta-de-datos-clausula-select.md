---
id: 77
title: 'Consulta de Datos &#8211; Cláusula Select'
author: Alejandro Alcalde
layout: post
guid: http://elbauldelprogramador.org/consulta-de-datos-clausula-select/
permalink: /consulta-de-datos-clausula-select/
blogger_blog:
  - www.elbauldelprogramador.org
  - www.elbauldelprogramador.org
  - www.elbauldelprogramador.org
blogger_author:
  - Alejandro Alcaldehttps://profiles.google.com/117030001562039350135noreply@blogger.com
  - Alejandro Alcaldehttps://profiles.google.com/117030001562039350135noreply@blogger.com
  - Alejandro Alcaldehttps://profiles.google.com/117030001562039350135noreply@blogger.com
blogger_permalink:
  - /2011/01/consulta-de-datos-clausula-select.html
  - /2011/01/consulta-de-datos-clausula-select.html
  - /2011/01/consulta-de-datos-clausula-select.html
categories:
  - BaseDeDatos
---
<div class="icosql">
</div>

A lo largo de varios post(enlazados entre ellos), vamos a ir viendo las distintas partes de las que se compone la sentencia SELECT, el motivo de hacer esto es que no salgan post demasiado largos para leer.

#### Consulta de Datos &#8211; Cláusula Select

La instrucción [DML][1] más utilizada es la de consulta de datos SELECT. Su función  
principal es la de recuperar filas de la tabla o tablas. Además, esta sentencia es capaz de realizar las siguientes funciones:  
  
<!--more-->

  * Obtener datos para la creación de una tabla.
  * Realizar operaciones estadísticas.
  * Definir cursores.
  * Realizar operaciones totalizadoras sobre grupos de valores que tienen los mismos  
    valores en ciertas columnas.
  * Se puede utilizar como sub-consulta para formar parte de una condición.

La sintaxis básica es:

{% highlight sql %}>SELECT select_list
FROM table_source
[WHERE search_condition]
[GROUP BY group_by_expression]
[HAVING search_condition]
[ORDER BY order_expression [ASC | DESC] ]
{% endhighlight %}

Vamos a ir viendo las diferentes clausulas que componen la sentencia SELECT:

  * Cláusula SELECT
  * [Cláusula FROM][2]
  * [Cláusula WHERE][3]
  * [Cláusula GROUP BY][4]
  * Cláusula HAVING
  * Cláusula ORDER BY



### Cláusula SELECT

Especifica qué columnas o expresiones han de ser devueltas por la consulta. Su sintaxis es:

{% highlight sql %}>SELECT [ DISTINCT ] &lt;select_list>

&lt;select_list> ::= [esquema.][table. | view. | alias. ] * | { column_name | expression }
[ [AS] column_alias ]} [,...n]
{% endhighlight %}

donde sus argumentos son:

#### DISTINCT

No aparecen los registros repetidos, mostrándose sólo la primera vez. Basta con que un  
único campo tenga diferente valor para que el registro se considere diferente.

#### <select_list>

<select_list> indica la lista de columnas de tablas, valores y/o expresiones que va a mostrar  
la sentencia SELECT. En este punto SQL en general es bastante más potente que otros lenguajes ya  
que nos permite mostrar junto con los campos: expresiones, algunas funciones especiales,  
agregados o funciones estadísticas especiales y constantes.

Dentro de esta lista se pueden mostrar los siguientes elementos:

{% highlight sql %}>table_name.* | view_name.* | table_alias.* | *{% endhighlight %}

En los cuatro casos con el uso del asterisco * selecciona todas las columnas de una tabla,  
vista o alias (renombrado).

#### column_name

Es el nombre de la columna que se devuelve. Para evitar o prevenir el problema que se  
presenta cuando dos columnas de diferentes tablas o relaciones se llamen igual se puede utilizar el cualificado del nombre de la columna anteponiendo el nombre de la tabla con un punto. SQL  
utiliza la notación:

{% highlight sql %}>nombre_Tabla.nombre_Atributo{% endhighlight %}

El uso de cualificados es obligatorio cuando en la <select_list> aparecen dos columnas con  
el mismo nombre.

En Oracle no es posible mezclar en la <select_list> el * con columnas y/o expresiones.  
Para realizar la operación anterior hay que cualificar el operador * con la tabla de la que se  
extraerán todos sus campos.

{% highlight sql %}>SELECT *, Subtotal FROM FACTURAS, LINFACTURAS;          -- Error
SELECT FACTURAS.*, Subtotal FROM FACTURAS, LINFACTURAS; -- Correcto
{% endhighlight %}



#### expression

Una expresión SQL está formada por columnas de las tablas de las bases de datos,  
operadores y las funciones disponibles en el entorno SQL.

#### column_alias

Es un nombre alternativo a una columna o a una expresión. Se utiliza normalmente sobre  
expresiones para asignarle un nombre que posteriormente pueda ser recuperado.

Ejemplo:

{% highlight sql %}>SELECT Cantidad*Precio AS Subtotal FROM LinFacturas{% endhighlight %}

El renombrado de columnas y/o expresiones puede ser usado en la cláusula ORDER BY;  
pero sin embargo no se puede usar en las cláusulas WHERE, GROUP BY, o HAVING.

* * *

#### Siguiente Tema: [Consulta de Datos &#8211; Cláusula FROM][2] {.referencia}

<div class="sharedaddy">
  <div class="sd-content">
    <ul>
      <li>
        <a class="hastip" rel="nofollow" href="http://twitter.com/home?status=Consulta de Datos &#8211; Cláusula Select+http://elbauldelprogramador.com/consulta-de-datos-clausula-select/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Twitter" target="_blank"><span class="iconbox-title"><i class="icon-twitter icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="http://www.facebook.com/sharer.php?u=http://elbauldelprogramador.com/consulta-de-datos-clausula-select/&t=Consulta de Datos &#8211; Cláusula Select+http://elbauldelprogramador.com/consulta-de-datos-clausula-select/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Facebook" target="_blank"><span class="iconbox-title"><i class="icon-facebook icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="https://plus.google.com/share?url=Consulta de Datos &#8211; Cláusula Select+http://elbauldelprogramador.com/consulta-de-datos-clausula-select/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en G+" target="_blank"><span class="iconbox-title"><i class="icon-google-plus icon-2x"></i></span></a>
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

 [1]: http://elbauldelprogramador.com/lenguaje-manipulacion-de-datos-dml/
 [2]: http://elbauldelprogramador.com/consulta-de-datos-clausula-from/
 [3]: http://elbauldelprogramador.com/consulta-de-datos-clausula-where/
 [4]: http://elbauldelprogramador.com/consulta-de-datos-clausula-group-by/