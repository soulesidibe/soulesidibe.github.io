---
layout: post
title: Android:Consommer un web service
---

Je vais vous parler de comment se fait la consommation de web services dans une application android. 
Pourquoi android? Deux raisons me pousse à parler des services sous cette plateforme mobile: 

* La presque totalité des jeunes étudiants fraîchement sortis de nos écoles d’informatique a eu à suivre un 
programme sur le développement avec le langage java (le langage de choix des profs pour appliquer les 
concepts de l’orienté objet). Et c’est se même langage qui est utilisé pour le développement sous android. 
* Parmi les plateformes mobiles à la mode, android offre une accessibilité "bon marché": 12.OOOFCFA ($25) 
pour avoir un compte développeur à vie, et les outils de développement sont accessible sur tous les systèmes 
d’exploitation et gratuitement. 

Avant de rentrer dans le vif du sujet, il est bon se mettre dans un contexte où utiliser des web 
services devient essentiel! Imaginons que vous avez développer une application (desktop, web ou autre) 
qui a fait le buzz! Et tout vas pour le mieux :-). Mais à un moment donné, vous vous rendez compte que votre 
application devrait pouvoir être utilisé pas que quand on est devant un PC. Et là vous commencez à penser à 
une application mobile sur les différentes plateforme star à savoir Android, IOS et Windows phone.

Un autre problème se pose. Comment allez vous faire pour mettre toute la logique de votre killer app dans 
un smartphone! déjà qu’il faudra une version par système d’exploitation, si on doit porter toute la logique 
sur chaque langage de programmation(java, objective-C, C#), bonjour la galère! 

Vous l’avez devenez! La solution c’est de garder la logique métier côté serveur et que chaque client
(mobile, web, desktop) fasse des appels quand il a besoin d’une ressource spécifique. Les web services REST 
sont une très bonne alternative pour mettre en place ce genre de technique.  Pas besoin d’une implémentation 
par langage; tous les clients utiliseront le protocole http pour communiquer avec les serveurs. Ce qui simplifie 
grandement les échanges entres les différentes entités. 

(La gestion des exceptions et certaines initialisations de variable ont été volontairement omises.) 

##Faire un appel

Pour faire un appel vu que REST est orienté web, on va utiliser les classes du package java.net. On crée un objet 
de type URL qui va contenir l’adresse de la ressource ou tout simplement le lien du service: 

{% highlight java %}
URL url = new URL("http://chemin/vers/service");
{% endhighlight %}

On ouvre une connexion

{% highlight java %}
URLConnection connection = url.openConnection();
HttpURLConnection httpConnection = (HttpURLConnection) connection;
{% endhighlight %}

Récupération du code de retour. Vu qu’on est en HTTP. Si tout c’est bien passé on doit recevoir le code 200 (ici HttpURLConnection.HTTP_OK) et enfin récupérer notre flux:

{% highlight java %}
int responseCode = httpConnection.getResponseCode();
if (responseCode == HttpURLConnection.HTTP_OK) {
   in = httpConnection.getInputStream();
}
{% endhighlight %}
A ce stade nous avons un objet de type InputStream qui contient les informations que le serveur a renvoyé.

##Parser du xml avec XMLPullParser

Apres la récupération des données il faut bien les lires. 
Pour cela il faudra utiliser un parseur en fonction de la techno utiliser pour envoyer les données côté serveur. 
Dans le cas du xml, il existe un parseur natif intégrer à Android: XMLPullParser. C’est un parseur bas niveau; 
c’est a dire qu’il faudra parcourir tout le document xml, tag par tag et faire des traitements en fonction du tag 
rencontré. Voici un exemple de xml que l’on peut recevoir: 

{% highlight xml %}
<personnes>
   <personne genre="feminin">
      <nom>Diallo</nom>
      <prenom>Binta</prenom>
      <age>15</age>
   </personne>
   <personne genre="masculin">
      <nom>Ndiaye</nom>
      <prenom>Alioune</prenom>
      <age>25</age>
   </personne>
</personnes>
{% endhighlight %} 

On aura besoin d’un objet String pour contenir la valeur d’un tag à chaque passage., un objet qui va nous 
fournir un parseur qui aura les paramètres indiqués( ici le support des espaces de noms, voir doc de **XmlPullParserFactory**), 
Et enfin générer notre parseur et lui spécifier l’objet InputStream avec lequel il devra travailler: 

{% highlight java %}
String valeur = null;
XmlPullParserFactory factory = XmlPullParserFactory.newInstance();
factory.setNamespaceAware(true);
XmlPullParser xpp = factory.newPullParser();
xpp.setInput(in, null);
{% endhighlight %} 

Voici un morceau de code tiré de la doc officiel qu’on peut utiliser pour extraire les informations du InputStream: 

{% highlight java %}
//Le type d'evenement pour savoir si on est au debut du xml et bien 
//d'autres informations
int eventType = xpp.getEventType();
//Tant qu'on n'est pas à la fin du document
while (eventType != XmlPullParser.END_DOCUMENT) {
   //Si on est au debut d'un tag
   if(eventType == XmlPullParser.START_TAG) {
      if (xpp.getName().equals("personne")) {
         genre = xpp.getAttributeValue(null, "genre");
      }
   } else if(eventType == XmlPullParser.END_TAG) {
      if (xpp.getName().equals("nom")){
         nom = valeur;
         valeur = null;
      } 
   } else if(eventType == XmlPullParser.TEXT) {
       //recuperation de la valeur pour le tag actuel
       valeur = xpp.getText();
   }
   eventType = xpp.next();
}
{% endhighlight %} 

Une petite astuce que vous avez surement remarqué, en début de tag on récupére les attributs du tag, en fin de 
tag on récupère la valeur pour ce tag. 
Un exemple concret sur ce parseur sur [github](https://github.com/soulesidibe/xmlPullParserExemple). 

##Manipuler du json

Pour traiter du json reçu via un web service, Android embarque en natif un package constituer de classe 
pour faire le job: **org.json.*** .Elles sont très simples d’utilisation. Voyons ce cas pratique de json: 

{% highlight json %}
{
   "personnes":[
      {
         "nom":"Fama",         
         "prenom":"Mbaye",         
         "age":"19",        
         "genre":"feminin"
      },
      {
         "nom":"Jack",
         "prenom":"Anthoine",
         "age":"30",
         "genre":"masculin"
      }
   ]
}
{% endhighlight %} 

Le code pour récupérer les informations ci-dessus: 

{% highlight java %}
//Convertir le InputStream de départ en String
BufferedReader bufferedReader = new BufferedReader(
    new InputStreamReader(in));
StringBuilder stringBuilder = new StringBuilder();
String uneLigneLue = bufferedReader.readLine();
while (uneLigneLue != null) {
   //on ajoute la nouvelle ligne lue dans le StringBuilder
   stringBuilder.append(UneLigneLue + "\n");
   //lecture de la ligne suivante
   ligneLue = bufferedReader.readLine(); 
}
bufferedReader.close();

//notre Objet maguique
JSONObject object = new JSONObject(stringBuilder.toString());
JSONArray array = object.getJSONArray("personnes");
for(int i = 0 ; i < array.length() ; i++){
    nom = array.getJSONObject(i).getString("nom");
}
{% endhighlight %} 

Un cas concret de manipulation du json sur [github](https://github.com/soulesidibe/JsonParserExemple). 

Cette facon de faire est pratique pour des fichiers aussi simple que celui utilisé mais il est mieux dans 
certains cas d’opter pour des librairies qui ajoutent un niveau d’abstraction dans un souci de simplifier 
la manipulation du json. C’est le cas de la librairie **GSON** qui permet de faire le passage du JSON en **POJO** 
et vice versa.  Il y a aussi la librairie **JACKSON** tout aussi puissante. 
Il faut juste choisir celle qui nous rend efficace. 

##Oui mais pourquoi faire?

Apres avoir reçu les données du serveur, après avoir convertir le xml/json en objet java, en tant que 
dévelopeur java, il n’y a pas vraiment de limite! 
Dans le contexte d’un développement android, il y a plusieurs manières d’utiliser ces données: 

* Mettre à jour une liste: exemple une vue qui affiche la timeline d’un compte twitter a besoin de récupérer 
les informations des serveurs de twitter pour les exposer à l’utilisateur via une ListView ou une ListActivity;
* Renseigner une base de données (sqlite) pour une utilisation de l’application en offline 

Les possibilités n’ont pas de limite. Ça dépendra de ce que l’application fait et de comment 
le développeur veut le faire.