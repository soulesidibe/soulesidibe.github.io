---
layout: post
title: Gradle Please!
---

Depuis son annonce au Google IO 2013, **Android Studio** est devenu mon IDE number 1! 
Je l’utilise au quotidien et tous mes projets eclipse ont migré. J’explique dans [ce billet](http://localhost:4000/2013/08/11/de-eclipse-a-android-studio/) comment faire la migration. 
L’utilisation de gradle comment système de build rend un projet android beaucoup plus simple à maintenir surtout en ce qui concerne la gestion des dépendances. 
Plus besoin d’importer des librairies à en plus finir! Un exemple d’ajout de dépendance: 

{% highlight groovy %}
dependencies {
   compile 'com.crashlytics.android:crashlytics:1.+'
}
{% endhighlight %}

Donc comme on le voit pour utiliser crashlytics c’est speed. Gradle se chargera de récupérer les fichiers qu’il faut et de les mettre dans le build path.

Maintenant, la plus part des projets open source qu’on utilise dans nos projets n’offrent pas cette possibilité(non disponibilité du aar). Donc voici un peu ce qu’on peut faire: 

- Demander au développeur de rendre son projet compatible avec gradle(sur github on peut facilement dialoguer avec le dév. En anglais),
- Si on ne peut pas patienter, on peut toujours récupérer le projet

`git clone chemin/vers/project.git project`, ajouter en tant que module dans le projet puis rajouter 
cette ligne dans build.gradle `compile project(':project')` et dans settings.gradle `include ':project'` 

Un site recense l’ensemble des projets open source qui ont été **"gradlisé"**. C’est [gradle please](http://gradleplease.appspot.com/)! :D