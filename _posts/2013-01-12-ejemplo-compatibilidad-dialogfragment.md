---
id: 1008
title: Crear DialogFragment compatibles con versiones inferiores a Android 3.0
author: Alejandro Alcalde
excerpt: Cómo crear dialogos de selección de fecha y hora en versiones inferiores a Android 3.0 con la librería de soporte.
layout: post
guid: /?p=1008
permalink: /crear-dialogfragment-compatibles-con-versiones-inferiores-a-android-3-0/
categories:
  - android
tags:
  - android librería de compatibilidad
  - android timepickerdialog dialogfragment example
  - curso android pdf
  - DataPickerDialog
  - DialogFragment
  - supportv4
  - TimeoPickerDialog
---
En un proyecto reciente he tenido que trabajar con las librerías de compatibilidad de Android, en este caso para crear diálogos que permitan elegir fecha y hora, como estos:

<img src="http://elbauldelprogramador.com/content/uploads/2012/11/pickers1.png" alt="" title="pickers" width="400" height="186" class="aligncenter size-full wp-image-1023" />

En Android recomiendan usar un `<a href="http://developer.android.com/reference/android/support/v4/app/DialogFragment.html" title="DialogFrgment" target="_blank">DialogFragment</a>`, que permite mostrar éstos diálogos en distintos layouts. Si pretendes que tu aplicación soporte este tipo de diálogos para versiones inferiores a Android 3.0, debes usar el DialogFragment mencionado anteriormente, si por lo contrario tu aplicación usa un *minSdkVersion* igual o superior a 11, puedes usar este otro `<a href="http://developer.android.com/reference/android/app/DialogFragment.html" target="_blank">DialogFragment</a>`. En este artículo se va a tratar la versión para soportar versiones anteriores a la 3.0.

#### Requisitos previos

Antes de empezar, es necesario descargar **<a href="http://developer.android.com/tools/extras/support-library.html" target="_blank">Support Library</a>** y agregarlas a nuestro proyecto Android (En eclipse, botón derecho en el proyecto &rsaquo;&rsaquo; propiedades &rsaquo;&rsaquo; Java Build Path &rsaquo;&rsaquo; lib). 

Otra forma de agregar la librería es hacer click derecho en el **proyecto » Android Tools » Add support library**.

Dicho esto, comencemos con el <a href="http://developer.android.com/reference/android/app/TimePickerDialog.html" target="_blank"><code>&lt;strong>TimePickerDialog&lt;/strong></code></a>  
  
<!--more-->

#### Crear un TimePickerDialog

El primer paso es crear una clase *fragment* que herede de *DialogFragment* y devuelva un `<em>TimePickerDialog</em>` desde el método <a href="http://developer.android.com/reference/android/support/v4/app/DialogFragment.html#onCreateDialog%28android.os.Bundle%29" target="_blank"><code>&lt;em> onCreateDialog()&lt;/em></code></a> del *fragment*:

{% highlight java %}>import android.app.Dialog;
import android.app.TimePickerDialog;
import android.os.Bundle;
import android.support.v4.app.DialogFragment;
import android.text.format.DateFormat;
import android.widget.TimePicker;

import java.util.Calendar;

public class TimePickerFragment extends DialogFragment implements
        TimePickerDialog.OnTimeSetListener
{

    @Override
    public Dialog onCreateDialog(Bundle savedInstanceState)
    {
        // Use the current time as the default values for the picker
        final Calendar c = Calendar.getInstance();
        int hour = c.get(Calendar.HOUR_OF_DAY);
        int minute = c.get(Calendar.MINUTE);

        // Create a new instance of TimePickerDialog and return it
        return new TimePickerDialog(getActivity(), this, hour, minute,
                DateFormat.is24HourFormat(getActivity()));
    }

    public void onTimeSet(TimePicker view, int hourOfDay, int minute)
    {
    }
}
{% endhighlight %}

Para cerciorarnos que se está usando la librería de compatibilidad, basta con ver el `import android.support.v4.app.DialogFragment;`

Por ahora dejaremos el método `onTimeSet` vacío; pasemos a crear la interfaz. A modo de ejemplo, crearemos un botón que muestre el dialogo cuando sea pulsado:

{% highlight xml %}>&lt;Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:onClick="mostrarDialogoDeTiempo"
        android:text="Diálogo de tiempo" />
{% endhighlight %}

Luego, creamos el método `mostrarDialogoDeTiempo` que será llamado al pulsar el botón:

{% highlight java %}>public void mostrarDialogoDeTiempo(View v) {
   DialogFragment newFragment = new TimePickerFragment();
   newFragment.show(getSupportFragmentManager(), "timePicker");
}
{% endhighlight %}

Llegados a este punto, es importante saber qué clase hemos de importar. Ya que el objetivo buscado es lograr compatibilidad entre las distintas versiones de android, para que la interfaz de la aplicación sea la misma en cualquier versión, la clase a importar es `import android.support.v4.app.DialogFragment;<br />
`. De lo contrario, sería `import android.app.DialogFragment;`  
Otro aspecto importante de cara a la compatibilidad, es la llamada a `<a href="http://developer.android.com/reference/android/support/v4/app/FragmentActivity.html#getSupportFragmentManager%28%29" target="_blank">getSupportFragmentManager()</a>` y que nuestra actividad herede de **FragmentActivity** en lugar de **Activity**.

Para comprobar que funciona, lanzamos el emulador, en este caso, con la versión 2.3 de Android:

[<img src="http://elbauldelprogramador.com/content/uploads/2013/01/device-2013-01-12-1337262-180x300.png" alt="TimePickerFragment Suportv4" width="180" height="300" class="aligncenter size-medium wp-image-1105" />][1]{.thumbnail}

El proceso de creación de un **DatePickerDialog** es muy similar.

#### Crear un DatePickerDialog

Definimos la clase:

{% highlight java %}>import android.app.DatePickerDialog;
import android.app.Dialog;
import android.os.Bundle;
import android.support.v4.app.DialogFragment;
import android.widget.DatePicker;

import java.util.Calendar;

public class DatePickerFragment extends DialogFragment
        implements DatePickerDialog.OnDateSetListener {

    @Override
    public Dialog onCreateDialog(Bundle savedInstanceState) {
        // Use the current date as the default date in the picker
        final Calendar c = Calendar.getInstance();
        int year = c.get(Calendar.YEAR);
        int month = c.get(Calendar.MONTH);
        int day = c.get(Calendar.DAY_OF_MONTH);

        // Create a new instance of DatePickerDialog and return it
        return new DatePickerDialog(getActivity(), this, year, month, day);
    }

    public void onDateSet(DatePicker view, int year, int month, int day) {
        // Do something with the date chosen by the user
    }
}
{% endhighlight %}

Al igual que antes, creamos un botón que muestre el diálogo:

{% highlight xml %}>&lt;Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:onClick="mostrarDialogoDeFecha"
        android:text="Diálogo de fecha" />
{% endhighlight %}

He implementamos el método que responderá al pulsar el botón:

{% highlight java %}>public void mostrarDialogoDeFecha(View v){
   DialogFragment newFragment = new DatePickerFragment();
   newFragment.show(getSupportFragmentManager(), "datePicker");
}
{% endhighlight %}

[<img src="http://elbauldelprogramador.com/content/uploads/2013/01/device-2013-01-12-1352432-180x300.png" alt="DateTimePicker supportv4 Android" width="180" height="300" class="aligncenter size-medium wp-image-1106" />][2]{.thumbnail}

Así de simple, es similar a crear un **timePickerDialog**  
El código de ejemplo está disponible para descarga:

#### Referencias

<a class="aligncenter download-button" href="http://elbauldelprogramador.com/download/ejemplo-compatibilidad-dialogfragment/" rel="nofollow"> Download &ldquo;Ejemplo compatibilidad DialogFragment&rdquo; <small>Ejemplo_datatimePickerFragment.zip &ndash; Downloaded 635 times &ndash; </small> </a>

  
*developer.android* **|** <a href="http://developer.android.com/guide/topics/ui/controls/pickers.html" target="_blank">Visitar sitio</a>

<div class="sharedaddy">
  <div class="sd-content">
    <ul>
      <li>
        <a class="hastip" rel="nofollow" href="http://twitter.com/home?status=Ejemplo compatibilidad DialogFragment+http://elbauldelprogramador.com/?post_type=dlm_download&p=2174+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Twitter" target="_blank"><span class="iconbox-title"><i class="icon-twitter icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="http://www.facebook.com/sharer.php?u=http://elbauldelprogramador.com/?post_type=dlm_download&p=2174&t=Ejemplo compatibilidad DialogFragment+http://elbauldelprogramador.com/?post_type=dlm_download&p=2174+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Facebook" target="_blank"><span class="iconbox-title"><i class="icon-facebook icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="https://plus.google.com/share?url=Ejemplo compatibilidad DialogFragment+http://elbauldelprogramador.com/?post_type=dlm_download&p=2174+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en G+" target="_blank"><span class="iconbox-title"><i class="icon-google-plus icon-2x"></i></span></a>
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

 [1]: http://elbauldelprogramador.com/content/uploads/2013/01/device-2013-01-12-1337262.png
 [2]: http://elbauldelprogramador.com/content/uploads/2013/01/device-2013-01-12-1352432.png