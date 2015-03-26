---
id: 2231
title: Cómo crear un diálogo personalizado en las preferencias Android
author: Alejandro Alcalde
layout: post
guid: http://elbauldelprogramador.com/?p=2231
permalink: /como-crear-un-dialogo-personalizado-en-las-preferencias-android/
categories:
  - android
tags:
  - dialogo personalizado android
  - dialogpreference
  - preferencias android
  - settings android
  - swadroid
---
Este año estoy participando en el <a href="http://www.concursosoftwarelibre.org/1314/" title="CUSL" target="_blank">concurso universitario de software libre</a>, concretamente en el proyecto <a href="http://swadroid.wordpress.com/" title="Blog SWADroid" target="_blank">SWADroid</a>. Mi intención es explicar todo lo que vaya aprendiendo mientras participo en el desarrollo del proyecto. Este primer artículo comienza un historial que llevará cuenta de todos los avances logrados.

La primera tarea que se me asignó fue lograr que el diálogo para introducir la contraseña de usuario en las preferencias se mostrara vacío. Ya que al [cifrar la contraseña para almacenarla][1] en el diálogo se mostraba la contraseña cifrada (Con los típicos •••), lo cual podía ser algo desconcertante para el usuario, ya que no veía un número de • que correspondieran con la longitud de su contraseña, si no con la contraseña cifrada.

Para explicar cómo conseguir este comportamiento escribiremos una aplicación trivial, enfocada únicamente a explicar todo el proceso.

<!--more-->

### Actividad Principal

El único propósito de esta [actividad][2] es abrir un menú que lanzará nuestras preferencias. Para simplificar el código se muestra sin las sentencias import y package:

{% highlight java %}>public class MainActivity extends Activity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }

    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.main, menu);
        return true;
    }
    
    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()) {
            case 0:
                startActivity(new Intent(this, MyPrefecenceActivity.class));
                break;

            default:
                break;
        }
        startActivity(new Intent(this, MyPrefecenceActivity.class));
        return true;
    }
}
{% endhighlight %}

En el método `onOptionsItemSelected` comprobamos qué elemento del [menú][3] se pulsó y abrimos la correspondiente preferencia, mostraremos a continuación el código para dicha actividad.

### Crear una Actividad para las Preferencias

{% highlight java %}>public class MyPrefecenceActivity extends PreferenceActivity implements OnPreferenceChangeListener {

    private Preference mDialogoNormal;
    private Preference mDialogoPersonalizado;
    private SharedPreferences.Editor mEditorDialogoNormal;
    private SharedPreferences.Editor mEditorDialogoPerso;
    private String mPasswordDialogoNormal;
    private String mPasswordDialogoPerso;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        addPreferencesFromResource(R.xml.preferences);
        mDialogoNormal = findPreference("diagNormal");
        mDialogoPersonalizado = findPreference("diagPerso");

        mDialogoNormal.setOnPreferenceChangeListener(this);
        mDialogoPersonalizado.setOnPreferenceChangeListener(this);

        mEditorDialogoNormal = mDialogoNormal.getEditor();
        mEditorDialogoPerso = mDialogoPersonalizado.getEditor();
        mPasswordDialogoNormal = mDialogoNormal.getSharedPreferences().getString("diagNormal", "");
        mPasswordDialogoPerso = mDialogoPersonalizado.getSharedPreferences().getString("diagPerso","");

        if (!mPasswordDialogoNormal.isEmpty())
            mDialogoNormal.setSummary("********");
        if (!mPasswordDialogoPerso.isEmpty())
            mDialogoPersonalizado.setSummary("********");
    }

    @Override
    public boolean onPreferenceChange(Preference preference, Object newValue) {

        String passw = newValue.toString();

        try {
            passw = cifrar(passw);
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        }

        if (preference.getKey().equals("diagNormal")) {

            mEditorDialogoNormal.putString("diagNormal", passw);
            mPasswordDialogoNormal = passw;
            mEditorDialogoNormal.commit();
            mDialogoNormal.setSummary("********");

        } else if (preference.getKey().equals("diagPerso")) {

            mEditorDialogoPerso.putString("diagPerso", passw);
            mPasswordDialogoPerso = passw;
            mEditorDialogoPerso.commit();
            mDialogoPersonalizado.setSummary("********");
        }

        return true;
    }

    @Override
    protected void onPause() {
        super.onPause();

        mEditorDialogoNormal.putString("diagNormal", mPasswordDialogoNormal);
        mEditorDialogoNormal.commit();
        
        mEditorDialogoPerso.putString("diagPerso", mPasswordDialogoPerso);
        mEditorDialogoPerso.commit();
    }

    public String cifrar(String passw) throws NoSuchAlgorithmException {
        String cifrada;
        MessageDigest md = MessageDigest.getInstance("SHA-512");
        md.update(passw.getBytes());
        cifrada = Base64.encodeToString(md.digest(), Base64.DEFAULT);

        return cifrada;
    }
}
{% endhighlight %}

Aunque esta no es la mejor forma de hacerlo, para ilustrar la diferencia entre ambas preferencias se almacenarán como dos objetos distintos. `mDialogoNormal` se comportará como una preferencia por defecto, en este caso es un `EditTtextPreference`, `mDialogoPersonalizado`, como su nombre indica, será la preferencia sobre la cual implementaremos nosotros el diálogo. En esta actividad que extiende de `PreferenceActivity` inicializamos todos los objetos necesarios en el `onCreate`. El método que realmente nos interesa es `onPreferenceChange`, que será llamado cuando se detecte algún cambio en los datos de las preferencias. En este caso se pretende mostrar cómo implementar una preferencia para almacenar una contraseña. Cuando el usuario introduce en el diálogo su contraseña, ésta será cifrada en el método `cifrar` y será éste el valor almacenado en el archivo de preferencias. Con el diálogo normal, si el usuario vuelve a lanzar el diálogo con una contraseña ya almacenada verá esto:

<img src="http://elbauldelprogramador.com/content/uploads/2014/01/CrearDialogoAndroid.png" alt="Crear DialogPreference personalizado Android" width="441" height="329" class="aligncenter size-full wp-image-2238" />

Lo cual corresponde a la contraseña cifrada, lo ideal sería que el cuadro de texto apareciera vacío, para ello crearemos nosotros mismos el diálogo extendiendo de `DialogPreference`.

{% highlight java %}>public class MyDialogPreference extends DialogPreference {

    private EditText mEditTextPassword;

    public MyDialogPreference(Context context, AttributeSet attrs) {
        super(context, attrs);
        setPersistent(false);
        setDialogLayoutResource(R.layout.dialog_password);
    }

    @Override
    protected void onDialogClosed(boolean positiveResult) {
        super.onDialogClosed(positiveResult);
    }

    @Override
    public void onClick(DialogInterface dialog, int which) {

        if (which == DialogInterface.BUTTON_POSITIVE) {
            String value = mEditTextPassword.getText().toString();
            callChangeListener(value);
        }
        super.onClick(dialog, which);
    }

    @Override
    protected void onBindDialogView(View view) {
        mEditTextPassword = (EditText) view.findViewById(R.id.etpPassword);

        super.onBindDialogView(view);
    }
}
{% endhighlight %}

En el constructor de esta clase es necesario establecer el layout que usará el diálogo, en este caso un simple `EditText`:

{% highlight xml %}><?xml version="1.0" encoding="utf-8"?>
&lt;LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >

    &lt;EditText
        android:id="@+id/etpPassword"
        android:name="DialogoPersonalidazo"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:defaultValue=""
        android:hint="dialogoPersonalizado"
        android:key="diagPerso"
        android:password="true"
        android:title="Dialogo Personalizado" />

&lt;/LinearLayout>
{% endhighlight %}

El método `callChangeListener` llamará a `onPreferenceChange`, donde decidiremos qué hacer con el valor de la preferencia, si guardarlo o descartarlo.

Con esto ya está todo listo, simplemente falta hacer referencia al diálogo que acabamos de crear en el archivo de preferencias de la siguiente forma:

{% highlight xml %}><?xml version="1.0" encoding="UTF-8"?>
&lt;PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android" >

    &lt;PreferenceCategory android:title="TituloCategoría" >
        &lt;EditTextPreference
            android:name="DialogoNormal"
            android:defaultValue=""
            android:hint="dialogonormal"
            android:key="diagNormal"
            android:password="true"
            android:title="Dialogo Normal" />

        &lt;com.example.dialogpreferenceexample.MyDialogPreference
            android:name="DialogoPersonalidazo"
            android:defaultValue=""
            android:hint="dialogoPersonalizado"
            android:key="diagPerso"
            android:password="true"
            android:title="Dialogo Personalizado" />
    &lt;/PreferenceCategory>

&lt;/PreferenceScreen>
{% endhighlight %}

Donde `com.example.dialogpreferenceexample.MyDialogPreference` es el nombre de la clase del diálogo personalizado.

#### Referencias

*Concise way of writing new DialogPreference classes?* **|** <a href="http://stackoverflow.com/a/4805325/1612432" target="_blank">stackoverflow.com</a>  
*Custom EditTextPreference and setOnPreferenceChangeListener not called* **|** <a href="http://stackoverflow.com/questions/13501099/custom-edittextpreference-and-setonpreferencechangelistener-not-called" target="_blank">stackoverflow.com</a>

<div class="sharedaddy">
  <div class="sd-content">
    <ul>
      <li>
        <a class="hastip" rel="nofollow" href="http://twitter.com/home?status=Cómo crear un diálogo personalizado en las preferencias Android+http://elbauldelprogramador.com/como-crear-un-dialogo-personalizado-en-las-preferencias-android/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Twitter" target="_blank"><span class="iconbox-title"><i class="icon-twitter icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="http://www.facebook.com/sharer.php?u=http://elbauldelprogramador.com/como-crear-un-dialogo-personalizado-en-las-preferencias-android/&t=Cómo crear un diálogo personalizado en las preferencias Android+http://elbauldelprogramador.com/como-crear-un-dialogo-personalizado-en-las-preferencias-android/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en Facebook" target="_blank"><span class="iconbox-title"><i class="icon-facebook icon-2x"></i></span></a>
      </li>
      <li>
        <a class="hastip" rel="nofollow" href="https://plus.google.com/share?url=Cómo crear un diálogo personalizado en las preferencias Android+http://elbauldelprogramador.com/como-crear-un-dialogo-personalizado-en-las-preferencias-android/+V%C3%ADa+%40elbaulp" onclick="javascript:window.open(this.href, '', 'menubar=no,toolbar=no,resizable=yes,scrollbars=yes,height=600,width=600');return false;" title="Compartir en G+" target="_blank"><span class="iconbox-title"><i class="icon-google-plus icon-2x"></i></span></a>
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

 [1]: http://elbauldelprogramador.com/como-se-almacenan-tus-contrasenas-en-internet-y-cuando-la-longitud-de-la-misma-no-importa/ "Cómo se almacenan tus contraseñas en internet (y cuando la longitud de la misma no importa)"
 [2]: http://elbauldelprogramador.com/fundamentos-programacion-android/ "Fundamentos programación Android: Conceptos básicos y componentes"
 [3]: http://elbauldelprogramador.com/programacion-android-interfaz-grafica_08/ "Programación Android: Interfaz gráfica – Menús"