---
id: 1013
title: 'Restlib &#8211; Librería para realizar peticiones a Web Services en Android'
author: Alejandro Alcalde
layout: post
guid: /?p=1013
permalink: /restlib-libreria-para-realizar-peticiones-a-web-services-en-android/
categories:
  - android
tags:
  - curso android pdf
  - json
  - libreria web services
  - restlib
  - web services android
  - web services programacion
---
Trabajando con un compañero en una aplicación que hacía uso de web services, nos planteamos la posibilidad de crear un librería que nos facilitara el desarrollo en aplicaciones similares. Aunque hay muchas disponibles en la red decidimos crear la nuestra propia. Gran parte de la librería está desarrollada por mi compañero <a href="http://menudoproblema.es/" target="_blank">Vicente</a>, yo crontibuí poco.

En estos días de navidad he decidido crear una aplicación que sirva como ejemplo de uso de la librería, y de paso he ido puliendo algunos aspectos de la misma que, dicho sea de paso, aún es bastante básica.

Por ahora solo permite JSON.

En la aplicación de ejemplo he trabajado con dos Web Services, el de **freegeoip**, para obtener ubicaciones en base a la dirección ip; y el de la API JSON de **WordPress**.

Usando estos dos web services he querido proporcionar dos ejemplos, ambos son peticiones **GET**, la diferencia reside en que uno es con parámetros y el otro no.

Empezaré con **freegeoip**, al ser la más simple. El código para armar la petición es el siguiente:  
  
<!--more-->

{% highlight java %}>RestRequest rq = new JSONRestRequest();
rq.setMethod(RestRequest.GET_METHOD);
rq.setURL("http://freegeoip.net/json/");

RestServiceTask task = new RestServiceTask(this, this, "Espere", "Obteniendo datos...");
task.execute(rq);
{% endhighlight %}

`rq` es el objeto necesario para construir la petición, en este caso irá en JSON. Las siguientes instrucciones establecen el típo de método a usar y la url del WebService, respectivamente. **RestServiceTask** se encarga de crear un <a href="http://developer.android.com/reference/android/os/AsyncTask.html" target="_blank">AsyncTask</a> para realizar la petición fuera del hilo principal de la aplicación.

En cada clase que se use la librería es necesario implementar la interfaz `AsyncTaskCompleteListener<RestResponse>`, Por ejemplo:

{% highlight java %}>public class MainActivity extends Activity implements AsyncTaskCompleteListener&lt;RestResponse>
{% endhighlight %}

Además, añadir el callback `onTaskComplete()`, que será llamado una vez obtengamos la respuesta del Web Service, la cabecera del método es la siguiente:

{% highlight java %}>@Override
    public void onTaskComplete(RestResponse result)
{% endhighlight %}

Para obtener la respuesta de la petición llamamos al método `getContent()` del objeto `result`, que devuelve un `HashMap<String, Object>` con los pares **clave/valor** correspondientes a la respuesta en JSON.

Cuando hay que proporcionar parámetros a la consulta, se debe crear un objeto HashMap que contendrá las claves y valores necesarios. En el caso del segundo ejemplo, la llamada se realiza al Web Service de test que proporciona WordPress. Algunos de los parámetros que acepta, entre otros, son *pretty=true|false, default\_string=cadena, http\_envelope=true|false, url=url*

Así pues, para realizar la petición en este caso el código sería el siguiente:

{% highlight java %}>RestRequest apiWordpress = new JSONRestRequest();
apiWordpress.setMethod(RestRequest.GET_METHOD);
apiWordpress.setURL("https://public-api.wordpress.com/rest/v1/test/5");
                
HashMap&lt;String, Object> args = new HashMap&lt;String, Object>() {
   {
      put("pretty", "true");
      put("default_string", "Test App WS www.elbauldelprogramador.org");
      put("http_envelope", "true");
      put("url", "http://www.elbauldelprogramador.org");
   }
};

apiWordpress.setContent(args);

RestServiceTask task2 = new RestServiceTask(this, this, "Espere", "Obteniendo datos...");
task2.execute(apiWordpress);
{% endhighlight %}

A continuación escribo el código de la aplicación de ejemplo que he programado para Android, en las referencias habrá un enlace para descargar el proyecto.

{% highlight java %}>package com.elbauldelprogramador.webservicelibexample;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.EditText;
import android.widget.TextView;
import android.widget.Toast;

import org.json.JSONException;
import org.json.JSONObject;

import thecocktaillab.restJsonLib.AsyncTaskCompleteListener;
import thecocktaillab.restJsonLib.JSONRestRequest;
import thecocktaillab.restJsonLib.RestRequest;
import thecocktaillab.restJsonLib.RestResponse;
import thecocktaillab.restJsonLib.RestServiceTask;

import java.util.HashMap;

public class MainActivity extends Activity implements AsyncTaskCompleteListener&lt;RestResponse> {

    private TextView ciudadD;
    private TextView codRegD;
    private TextView nombreRegD;
    private TextView metroCodeD;
    private TextView zipD;
    private TextView longiD;
    private TextView paisD;
    private TextView codPaisD;
    private TextView ipD;
    private TextView latdD;
    private EditText wordpress;
    private boolean testingGeoIp;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        ciudadD     = (TextView) findViewById(R.id.ciudadD);
        codRegD     = (TextView) findViewById(R.id.codRedD);
        nombreRegD  = (TextView) findViewById(R.id.nombreRegD);
        metroCodeD  = (TextView) findViewById(R.id.metroCodeD);
        zipD        = (TextView) findViewById(R.id.zipD);
        longiD      = (TextView) findViewById(R.id.longiD);
        paisD       = (TextView) findViewById(R.id.paisD);
        codPaisD    = (TextView) findViewById(R.id.codPaisD);
        ipD         = (TextView) findViewById(R.id.ipD);
        latdD       = (TextView) findViewById(R.id.latD);
        wordpress   = (EditText) findViewById(R.id.wordpress);
        
    }

    public void onClickHandler(View target) {
        switch (target.getId()) {
            case R.id.testFreegeoip:
                
                testingGeoIp = true;
                
                RestRequest rq = new JSONRestRequest();
                rq.setMethod(RestRequest.GET_METHOD);
                rq.setURL("http://freegeoip.net/json/");

                RestServiceTask task = new RestServiceTask(this, this, "Espere", "Obteniendo datos...");
                task.execute(rq);
                break;

            case R.id.testWordpress:

                testingGeoIp = false;
                
                RestRequest apiWordpress = new JSONRestRequest();
                apiWordpress.setMethod(RestRequest.GET_METHOD);
                apiWordpress.setURL("https://public-api.wordpress.com/rest/v1/test/5");
                
                HashMap&lt;String, Object> args = new HashMap&lt;String, Object>() {
                    {
                        put("pretty", "true");
                        put("default_string", "Test App WS www.elbauldelprogramador.org");
                        put("http_envelope", "true");
                        put("url", "http://www.elbauldelprogramador.org");
                    }
                };

                apiWordpress.setContent(args);

                RestServiceTask task2 = new RestServiceTask(this, this, "Espere", "Obteniendo datos...");
                task2.execute(apiWordpress);
                break;
                
            default:
                break;
        }
    }
    
    @Override
    public void onTaskComplete(RestResponse result) {
        JSONObject r = new JSONObject(result.getContent());

        Toast.makeText(this, r.toString(), Toast.LENGTH_LONG).show();

        try {
            if (testingGeoIp) {
                ciudadD.setText(r.getString("city"));
                codRegD.setText(r.getString("region_code"));
                nombreRegD.setText(r.getString("region_name"));
                metroCodeD.setText(r.getString("metrocode"));
                zipD.setText(r.getString("zipcode"));
                longiD.setText(r.getString("longitude"));
                latdD.setText(r.getString("latitude"));
                codPaisD.setText(r.getString("country_code"));
                ipD.setText(r.getString("ip"));
                paisD.setText(r.getString("country_name"));
            }
            else {
                wordpress.setText(r.toString());
            }

        } catch (JSONException e) {
            e.printStackTrace();
        }

    }

}
{% endhighlight %}

Es necesario agregar la librería al proyecto, para ello, descárgala, crea una carpeta en tu proyecto llamada **libs**. En eclipse; **clic derecho en dicha carpeta » import » File System**, selecciona la carpeta en la que se encuentra la librería y en la parte derecha selecciónala; pulsa finalizar. Luego, en las **propiedades del proyecto » Java Build Path » Libraries » Add JARs**. Ya está agregada al proyecto y lista para usar.

La aplicación de ejemplo debe quedar así:

[<img src="http://elbauldelprogramador.com/content/uploads/2013/01/webservicelibexample2.png" alt="WebserviceLibExample" width="480" height="800" class="aligncenter size-full wp-image-1089" />][1]{.thumbnail}

Para finalizar, decir que la librería por ahora está muy limitada, pero es perfectamente funcional para realizar peticiones básicas. Intentaremos seguir desarrollandola cuando dispongamos de más tiempo.

#### Referencias

<a class="aligncenter download-button" href="http://elbauldelprogramador.com/download/webservicelibexample/" rel="nofollow"> Download &ldquo;WebserviceLibExample&rdquo; <small>WebserviceLibExample.zip &ndash; Downloaded 811 times &ndash; </small> </a>

*FreeGeoIp* **|** <a href="http://freegeoip.net/static/index.html" target="_blank">Visitar sitio</a>  
*developer.wordpress.com* **|** <a href="http://developer.wordpress.com/docs/api/1/get/test/%24ID/" target="_blank">Visitar sitio</a>  
*Descargar librería* **|** <a href="https://github.com/Cocktails/Restlib/blob/master/restlib.jar" target="_blank">Visitar sitio</a>  
*GitHub del proyecto* **|** <a href="https://github.com/Cocktails/Restlib" target="_blank">Visitar sitio</a>

<div class="sharedaddy">
  <div class="sd-content">
    <ul>
      <li>
        <a class="hastip" rel="nofollow" href="http://twitter.com/home?status=WebserviceLibExample+http://elbauldelprogramador.com/?post_type=dlm_download&p=2171+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Twitter" target="_blank"><span class="iconbox-title"><i class="icon-twitter icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="http://www.facebook.com/sharer.php?u=http://elbauldelprogramador.com/?post_type=dlm_download&p=2171&t=WebserviceLibExample+http://elbauldelprogramador.com/?post_type=dlm_download&p=2171+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Facebook" target="_blank"><span class="iconbox-title"><i class="icon-facebook icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="https://plus.google.com/share?url=WebserviceLibExample+http://elbauldelprogramador.com/?post_type=dlm_download&p=2171+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en G+" target="_blank"><span class="iconbox-title"><i class="icon-google-plus icon-2x"></i></span></a>
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

 [1]: http://elbauldelprogramador.com/content/uploads/2013/01/webservicelibexample2.png