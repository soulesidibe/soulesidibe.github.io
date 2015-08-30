---
layout: post
title: Android 6.0 Marshmallow est la! (Part 1)
---

![android 6.0](/assets/marshmallow.jpg)
L'équipe Android a annoncé il y a quelques jours le nom de la prochaine version. On savait que le nom commencerait par __M__. Mais la c'est dit. Let me introduce you __Marshmallow__! Pour l'occasion on passe à __android 6.0 API version 23__. Donc vous savez ce que vous avec à faire dès maintenant:

{% highlight groovy %}
android {
    compileSdkVersion 23
    ...
    defaultConfig {
        targetSdkVersion 23
        ...
    }
    ...
}
{% endhighlight %}

Il s'agit de la preview 3 du sdk. D'après ce qu'ils avaient dit lors de la dernière Google IO, la preview 3 est la version finale du SDK. Ce qui veut dire qu'on peut se baser sur les APIs fournies sans craindre de nouvelles modifications. La version finale sortira lors du Q3 de cette année 2015. Soon!!!

##Installer android 6.0

Pour les nexus owners désirant tester la nouvelle version sur un vrai device, les images sont disponibles pour les nexus 5, nexus 6, nexus 9 et nexus player au niveau du site [officiel](http://developer.android.com/preview/download.html).
Sinon, après mise à jour du SDK depuis android studio, il est possible de créer des AVDs tournant sous Marshmallow.

##Quoi de neuf?

Android Marshmallow n'apporte pas énormement de chose comme Android Lollipop(avec son material design et cie). D'ou mon incomprehesion du passage à une version 6.0 au lieu de 5.2 par exemple.  

#####[App Links](http://developer.android.com/preview/features/app-linking.html)

App links est une nouvelle façon de gérer les liens web sur un smartphone tournant sous android 6.0 et plus. Ca va permettre de pouvoir ouvrir directement l'application au lieu de lancer le navigateur par defaut ou de proposer une liste d'applications pouvant gérer le lien.  
Donc une meilleur experience pour un utilisateur qui aura moins d'actions à faire pour accéder au contenu. 
Comment le mettre en place?  

App Links utilise les Intents pour gérer les requêtes d'ouverture d'URLs. Donc pour dire au système que tel URL doit être ouvert par mon app, on rajoute un intent-filer dans le manifest de votre app:

{% highlight xml %}
<activity ...>
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="http" />
        <data android:scheme="https" />
        <data android:host="www.android.com" />
    </intent-filter>
</activity>
{% endhighlight %}

Là, on lui dit que le lien `www.android.com`, en utilisant le protocole `http` ou `https` est lié à cette application.
Maintenant, s'il suffit de rajouter cela à notre manifest, n'importe qui pourra lier son app à un site qui ne lui appartient pas. Ils ont évidemment pensé à cela. Le domaine déclaré dans le manifest doit hoster un fichier json(`assetlinks.json`) ayant les informations permettant de vérifier l'app voulant être liée à ce domaine. Dans notre cas `www.android.com`. 
Ce fichier json doit être localisé suivant un pattern défini: 
`https://domain[:optional_port]/.well-known/assetlinks.json`. 
Et voici sont contenu:  

{% highlight json %}
[{
    "relation": ["delegate_permission/common.handle_all_urls"],
    "target": {
      	"namespace": "android_app",
      	"package_name": "com.example",
      	"keytool -list -v -keystore my-release-key.keystore":
      	["14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5"]
    }
}]
{% endhighlight %}

Les informations les plus importantes de ce json sont le `package_name` qui est le package name de l'application, et le `sha256_cert_fingerprints` qui représente l'empreinte SHA256 de la clé de signature de l'app. Et pour l'obtenir: `keytool -list -v -keystore my-release-key.keystore`. A noter qu'il est possible d'avoir plusieurs clé 'sha256_cert_fingerprints' dans le même fichier json. Ce qui est utile quand on a plusieurs variantes de notre application.

C'est tout pour cette premiere partie. Je parlerai du nouveau __systeme de permission__ lors de la partie 2. 

Peace!