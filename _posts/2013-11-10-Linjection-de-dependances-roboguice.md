---
layout: post
title: L’injection de dépendances:roboguice
---

Je ne vais pas vous faire un cours sur l’injection de dépendances en java. Il y a de très bons livres 
et de bonnes ressources sur le net. 
Mais ce que je peux vous dire en gros, l’injection de dépendances va nous permettre de manager nos objects, 
nos classes en vu d’un code plus claire et maintenable. 
Pour vous donner un exemple de ce qu’on peut faire avec, on a l’habitude de faire ceci pour instancier 
une view: 

{% highlight java %}
Button button = (Button)findViewById(R.id.button); 
{% endhighlight %}
Avec de l’injection de dépendances on aura qu’à faire ceci: 

{% highlight java %}
@InjectView(R.id.button) Button button; 
{% endhighlight %}

Pas mal non? :D 
Donc comme je l’ai dit ça va grandement simplifier notre code java.

##Comment faire de l’injection de dépendances 

Comme d’habitude dans le monde java, il existe un bon paquet de librairies open source pour nous 
simplifier la vie. On aura pas besoin d’implémenter nous même notre système d’injection de 
dépendances. On peut qu’en même retenir les types d’injections de dépendances qui existent:

* l’injection par constructeur 
* l’injection par interface 
* l’injection par mutateur 
* l’injection par champ 

Pour plus de détails je vous renvoie vers [ce lien](http://www-igm.univ-mlv.fr/~dr/XPOSE2010/guicespring/di_presentation.html) qui explique assez clairement les choses… 

##Roboguice

>RoboGuice is a framework that brings the simplicity and ease of Dependency Injection to Android, using Google’s own Guice library. If you’ve ever used Spring (the #1 enterprise framework on Java, now more popular than J2EE itself) or Guice, you already know how convenient this style of programming can be.

Définition tirée du wiki du framework. 
En résumé(ou pour traduire), roboguice permet d’adapter le framework d’injection de dépendances 
**Google Guice** au monde android tout en y ajoutant des subtilités dues au cycle de vie particulier 
d’une application android.
Voici une liste non exhaustive des fonctionnalités de roboguice: 

* L’injection de view (@InjectView)
* l’injection de ressources(@InjectResource)
* l’injection de service(par exemple pour NotificationManager, on aura @Inject NoificationManager notifManager;)
* l’injection de pojo

et bien d’autres bonnes choses…

##Comment ça marche

Je vais vous montrer quelques fonctionnalités de roboguice. Le code est disponible sur github(see below…)
On commence par se faire un petit projet dans android studio(regarde ceci si tu n’utilise pas encore l’IDE).
Je suppose que vous êtes prêt  donc allons-y! 

NB: _Le projet que j’utilise a pour min SDK API 14 et pour target API 19 raison pour laquelle je n’ai pas pu injecter de view dans des fragments. Roboguice ne supportant pour le moment que la class Fragment du support V4._ 

Ajout de la dépendances dans le fichier build.gradle: 

{% highlight groovy %}
dependencies {
    compile 'org.roboguice:roboguice:2.0'
} 
{% endhighlight %}

Dans votre activity, vous devez hériter de RoboActivity à la place de Activity. Jettez un coup d’oeil 
dans le package **roboguice.activity** pour voir un peu les types d’activity disponibles.

Voici à quoi ressemble mon fichier layout. C’est un simple écran de connexion avec deux EditText 
et un Button de connexion: 

{% highlight xml %}
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
	xmlns:tools="http://schemas.android.com/tools"
	android:layout_width="match_parent"
	android:layout_height="match_parent"
	tools:context=".MainActivity">

		<EditText
			android:id="@+id/edt_name"
			android:singleLine="true"
			android:hint="@string/str_login_name"
			android:layout_height="wrap_content"
			android:layout_width="wrap_content"
			android:layout_centerVertical="true"
			android:layout_alignParentRight="true"
			android:layout_alignParentLeft="true" />

		<EditText
			android:singleLine="true"
			android:id="@+id/edt_passwd"
			android:inputType="textPassword"
			android:hint="@string/str_login_name"
			android:layout_height="wrap_content"
			android:layout_width="wrap_content"
			android:layout_marginTop="30dp"
			android:layout_below="@+id/edt_name"
			android:layout_alignParentLeft="true"
			android:layout_alignParentRight="true" />

		<Button
			android:id="@+id/btn_connect"
			android:text="@string/str_button_signin"
			android:layout_height="wrap_content"
			android:layout_width="wrap_content"
			android:layout_alignParentBottom="true"
			android:layout_alignParentRight="true"
			android:layout_alignParentLeft="true" />
</RelativeLayout>
{% endhighlight %}

Ma class MainActivity.java: 

{% highlight java %}
public class MainActivity extends RoboActivity {

    @InjectView(R.id.edt_name)
    EditText name;
    @InjectView(R.id.edt_passwd)
    EditText passwd;
    @InjectView(R.id.btn_connect)
    Button signin;

    @InjectResource(R.string.str_welcome)
    String welcome;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
         super.onCreate(savedInstanceState);
         setContentView(R.layout.activity_main);

         signin.setOnClickListener(new View.OnClickListener() {
         @Override
             public void onClick(View v) {
                 String strName = " " + name.getText().toString();
                 Toast.makeText(MainActivity.this, welcome + strName, Toast.LENGTH_SHORT).show();
             }
        });
   }
}
{% endhighlight %} 

That’s it! C’est super simple à utiliser. Il ne faut juste pas oublier qu’on utilise **RoboActivity** 
à la place de Activity! Sinon, on se retouve avec des java.lang.NullPointerException!
Regardons un peu notre méthode onCreate(). Elle s’est grandement simplifiée. Et il faut reconnaitre 
que ce code ci-dessus est plus facile à lire et à maintenir que si on devait faire ceci: 

{% highlight java %}
public class MainActivity extends Activity {
    private EditText name;
    private EditText passwd;
    private Button signin;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        name = (EditText) findViewById(R.id.edt_name);
        passwd = (EditText) findViewById(R.id.edt_passwd);
        signin = (Button) findViewById(R.id.btn_connect);
        final String welcome = getResources().getString(R.string.str_welcome);

        signin.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v){
                String strName = " " + name.getText().toString();
                Toast.makeText(MainActivity.this, welcome + strName, Toast.LENGTH_SHORT).show();
            }
        });
    }
}
{% endhighlight %} 

Vous êtes convaincus? Non? Bon voici quelques exemples d’applications utilisant Roboguice:

* Facebook Messenger
* Pulse
* Swiftkey
* Google Docs

Pas mal non?
Bon la librairie n’est pas parfaite non plus. Ce que je n’ai pas aimé:

Si on utilise des fragments et qu’on a comme minSDKVersion 14, on ne peut tout simplement pas faire d’injections dans un fragment. La raison est que la classe RoboFragment, fournie, utilise android.support.v4.app.Fragment.
Le non support de l’ActionBarCompat
Heureusement que ces soucis seront corrigés dans les prochaines release! 

##Alternative à Roboguice

Il existe d’autres solutions pour faire de l’injection de dépendances. Je vais en citer deux!

###Dagger

C’est une solution issue des labos de la célébre startup square. Elle permet d'injecter tout ce que l'on veut
dans notre application. Pas uniquement des vues.
Pour plus d’informations [le site officiel](http://square.github.io/dagger/) 

###AndroidAnnotation

Je pourrai écrire un post sur ce framework tellement il y a des choses à dire. Déjà le but de cette 
librairie d’après les concepteurs:

>We want to facilitate the writing and the maintenance of Android applications.
 We believe that simple code with clear intents is the best way to achieve those goals. 

Pour atteindre leurs objectifs, on n’y trouve évidemment de l’injection de dépendances. Avec de simples annotations, 
on peut faire des appels **REST**, exécuter du code en background, mettre en place des listeners avec *@Click*, etc. 
Autre point fort, il supporte RoboGuice :). 
Un truc sympa avec android annotation, supposons qu’on ait une vue avec comme id, _@+id/signin_. Pour la récupérer dans le code il suffit de faire:

{% highlight java%}
@ViewById
ListView signin;
{% endhighlight %}

Pas besoin de lui spécifier l’id comme avec Roboguice. Il fera le mapping avec R.id.signin tout seul :).

Voila voila!
Si vous avez ce souci de vouloir toujours un code plus propre et maintenable, je vous conseille vivement 
de revoir Roboguice ou plus généralement l’injection de dépendances.
Le petit exemple de prise en main est dispo sur [github](https://github.com/soulesidibe/roboguicesample).