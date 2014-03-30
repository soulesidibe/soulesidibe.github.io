---
layout: post
title: De eclipse à android Studio
---

Comme vous le savez tous, lors du google IO 2013, il y a eu beaucoup de nouvelles concernant les développeurs android. 
Je ne vais pas toutes les lister ici mais juste celle qui nous intéressent pour l’article du jour. 
La team android nous a donc annoncé Android Studio! La nouvelle bête pour nos dévs en remplacement de l’IDE éclipse. 

Vous me direz ouff enfin un IDE dédié au développement android! Yes ils se sont enfin décidé. 
La seconde grosse annonce fut le remplacement du système de build ant par gradle. 
[**ICI**](http://tools.android.com/tech-docs/new-build-system/user-guide#TOC-Goals-of-the-new-Build-System) les raisons pour lesquelles la team android l’a choisi. 

Ok mais le souci est que tous nos projets actuels sont sous eclipse. Maintenant comment faire migrer ces projets 
la vers SA et gradle? Google nous a fourni une démarche à suivre sur le site officiel. Pour un projet simple ca peut passer. Mais quand on a des projets librairies par ici ou des jars à en plus finir, ca devient un vrai casse-tête pour arriver à cette image:

![Android Studio](/assets/capture-du-2013-08-11-140412.png)

Mais bon on est la pour atteindre cette objectif :-). 
Il nous faut, avant de commencer, un projet concret où on retrouvera quelques librairies(sous forme de project library 
ou de jar). Pour vous faire gagner du temps en voici un petit projet héberger sur [githtub](https://github.com/soulesidibe/KillApp). 

Donc, on y trouve un projet utilisant les librairies ActionBarSherlock, PullToRefresh et deux jars l’un le support-v4 
et une petite lib permettant d’utiliser l’api de bitly. Il vous suffit maintenant de cloner le projet et de redemarrer votre eclipse en prenant pour workspace le dossier créé lors du clone et enfin commencer :-). 

##Mettre à jour ADT

Pour faire la migration nous avons besoin d’un outil proposé par la dernière version du plugin ADT et permettant 
de générer les fichiers utilisés par gradle pour construire notre projet. 
D’où la nécessité de mettre à jour le plugin mais aussi votre sdk pour bénéficier des derniers "tools".

##gradle.build, settings.gradle

Il est temps maintenant d’exporter notre projet! La procédure est très simple! File -> Export et on a cette fenêtre qui s’affiche: 

![Eclipse adt](/assets/capture-du-2013-08-11-151318.png)

Donc on choisit "Generate gradle build files": 

![Eclipse adt](/assets/capture-du-2013-08-11-151541.png)

La il suffit de sélectionner le projet et non les dépendances du projet. Ici KillerApp. 
Le reste c’est du suivant suivant… A ce stade, eclipse nous a généré tous les fichiers qu’il faut. 
Quelques explications s’imposent. 
Nous avons l’apparition de quelques fichiers à la racine de notre projet mais aussi dans les répertoires de chaque module.  Oops! je ne vous ai pas dit! Avec AS ou IntellIJ, le workspace de Eclipse devient un projet, le projet de Eclipse devient un module. [Plus de détails ici](http://confluence.jetbrains.com/display/IntelliJIDEA/FAQ+on+Migrating+to+IntelliJ+IDEA). Que représentent ces fichiers?

![terminator](/assets/capture-du-2013-08-11-152554.png)

* gradle/: ce répertoire est une "installation locale" de l’outil gradle. on a donc pas besoin de l’installer sur notre pc. C’est la pratique qui est conseillé pour etre sure d’avoir tout le temps la bonne version de gradle pour builder nos projets
* gradlew, gradlew.bat: c’est le script qui nous permet de lancer les builds sur notre projet en ligne de commande au lieu de passer par Android Studio. Par exemple, pour générer un apk on peut lancer la commande ./gradlew clean assemble
* build.gradle et settings.gradle sont les deux fichiers où on passera le plus de temps pour configurer nos projets et leurs dépendances. Ce sont les fichiers de configurations.

##Android Studio

On peut maintenant importer notre projet sur AS en choisissant le fichier build.gradle situé à la racine du projet. 
Apres quelques temps(lecture des fichiers de conf, résolution des dépendances, etc), nous avons enfin AS qui se lance avec notre projet KillerApp ouvert! Et si on essayait de lancer l’app pour voir un peu?

Bon évidemment ça ne passe pas. Gradle nous signifie qu’il y a des erreurs un peu partout et quand on regarde de plus prêt, nos librairies en sont pour quelque chose… 

Commençons par regarder le contenu des fichier build.gradle et settings.gradle de la racine.
settings.gradle: 

{% highlight groovy %}
include ':pull_to_refresh'
include ':ABS'
include ':KillerApp'
{% endhighlight %}

Donc on comprends que c’est dans ce fichier qu’on déclare les modules à inclure dans le build.
Le build lui même est configuré dans le fichier du même nom, build.gradle: 

{% highlight groovy %}
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:0.5.+'
    }
}
{% endhighlight %}

Il est assez simple comme fichier. La raison de cette simplicité est que la tendance est à avoir une convention de base dans la façon de construire un apk. Ce que l’on rajoute dans le fichier de configuration ce sont les informations qui font que le projet est spécifique. À savoir, les dépendances, l’emplacement de certains dossiers sources, etc. 
Ici, une dépendance au plugin android pour gradle en version 0.5 au moins. 

Autre détail, les fichiers build et settings de nos modules héritent des paramètres de deux de la racine. Donc on aura pas besoin de déclarer à nouveau une dépendance vers gradle 0.5. On comprend que c’est le lieux pour toutes configurations applicables à l’ensemble du projet. C’est le cas des paramétres comme le minSdk, le buildToolsVersion, etc. Ces informations sont souvent source de "fail" lors d’un build. 

On a donc:  

{% highlight groovy %}
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:0.5.+'
    }

    ext.compileSdkVersion=18
    ext.buildToolsVersion="18.0.0"
    ext.minSdkVersion=8
    ext.targetSdkVersion=18
}
{% endhighlight %}

Passons aux dépendances du module ActionBarSherlock. Il a besoin de la librairie android support-v4. Il y a deux façons de procéder: 

* soit on met le fichier jar dans le dossier libs et on rajoute ceci dans son build.gradle: 
	{% highlight groovy %}
  	dependencies {
    	compile fileTree(dir: 'libs', include: '*.jar')
	}
	{% endhighlight %}

* soit on utilise la pleine puissance de maven pour qu’il nous gére nos dépendances sans qu’on n’ait à se préoccuper des fichiers jars. Dans ce cas il suffira de rajouter ces quelques lignes: 
	{% highlight groovy %}
  	repositories {
    	mavenLocal()
	}
	dependencies {
    	compile 'com.android.support:support-v4:18.0.0'
	}
	{% endhighlight %}
Ainsi, on peut se débarrasser du dossier libs. gradle se chargera d’aller chercher le bon fichier jar et de l’inclure au classpath de notre projet.

Pour en savoir un peu plus sur maven, je vous recommande de lire cette série d’articles.
Il ne faudra pas oublier de remplacer ceci: 

{% highlight groovy %}
compileSdkVersion 14    
buildToolsVersion "18.0.1"
{% endhighlight %}

par:

{% highlight groovy %}
compileSdkVersion rootProject.compileSdkVersion
buildToolsVersion rootProject.buildToolsVersion
{% endhighlight %}

dans tous les modules du projet.
Pour rappel, on avait dit que certains paramètres devront être les mêmes pour tous les modules du projet par souci de cohérence. 

Petite info que j’ai sauté, pour faire la diferrence entre une librairie et un module, il suffit d’appliquer le plugin qu’il faut. Donc pour ActionBarSherlock on aura
`apply plugin: ‘android-library’` et pour le module KillerApp `apply plugin: ‘android’` dans le fichier build.gradle. "android-library" génére un aar(Android Archive) alors que "android" génére un APK. 

Le module PullToRefresh n’a besoin d’aucune dépendance spécifique donc on peut s’attaquer au module KillerApp. Voici ce qu’on rajoutera

{% highlight groovy %}
repositories {
    mavenLocal()
}
dependencies {
    compile 'com.android.support:support-v4:18.0.0'
    compile files(dir: 'libs', include: '*.jar')
    compile project(':ABS')
    compile project(':pull_to_refresh')
}
{% endhighlight %}

On lui spécifie qu’il aura besoin du support-v4 et des librairies ABS et pull_to_refresh présentes à la racine.
On y est! Tout a été configuré, maintenant il suffit de faire un  build pour voir que tout compile comme il faut! 

![AS](/assets/capture-du-2013-08-11-204553.png)

Nous venons de voir qu’il n’est pas très compliqué de passer de Éclipse à Android Studio. Il suffit juste d’avoir des connaissances sur gradle, maven et de se familiariser un peu avec l’IDE. 

Vu que l’article n’avait pas pour objectif de vous donner ces bases, je vous recommande fortement d’aller lire en entier cette [série d’articles](http://blog.stylingandroid.com/archives/1872) sur gradle(7 en tout) et aussi de lire la [documentation](http://tools.android.com/tech-docs/new-build-system/user-guide) fournie par google(pour une utilisation avancée). 
Avec cela vous pourrez avoir un usage professionnel de ces outils assez puissants :D.