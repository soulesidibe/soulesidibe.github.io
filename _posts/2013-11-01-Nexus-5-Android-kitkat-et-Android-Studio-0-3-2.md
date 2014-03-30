---
layout: post
title: Nexus 5, Android kitkat et Android Studio 0.3.2
---

Hier on a eu droit à un bon paquet de nouveautés dans le monde merveilleux d’android.
Je vais essayer de revoir ce qui s’est passé :). 

##Nexus 5

![nexus5](/assets/lg-nexus-5-photo.png)

Enfin! 
Depuis un bon moment on nous parle de ce nouveau nexus sur les sites spécialisés. Il est là! Et il est beau! 
La première chose qui frappe c’est la taille de la bête; un écran de 5 pouces(4.95" en réalité) full HD équipé 
de la dernière version d’android(on en parle en bas). 
Point important, il est 4G! Comme tous les smartphones récemment sortis. Pour plus de détails c’est ici ==> [Page officielle](http://www.google.fr/nexus/5/). 
Ayant un **#nexus4**, quand je revois les spécifications techniques du **#nexus5**, je me dis que c’est juste 
une mise à jour du matériel avec un meilleur capteur photo, la 4G et un sublime écran. 
Perso avoir un smartphone de 5 pouces ne me tente pas vraiment même si je pense que c’est la taille maximal pour un tel. 
Si vous êtes un nexus fan boy et que vous avez un nexus 4(et qu’il est en bonne état), vous pouvez vous passer du nexus 5. 
Surtout que vous recevrez bientôt la mise à jour KitKat… 

##Android KiKat 4.4

![kitkat](/assets/android-kitkat.jpg)

Celui la on la vraiment attendu et il n’a pas déçu! Cette version android est annoncée comme celle qui 
va permettre de réduire encore plus la fragmentation du système Android. En effet, d’après les dires de 
[Sundar Pichai](https://plus.google.com/+SundarPichai), Kitkat pourra tourner sur beaucoup plus de smartphones android et non que sur les derniers. 
Il suffira d’un device avec au moins **512Mo** de Ram. Ce qui va permettre de reduire encore plus vite la 
part de [Gingerbread](http://goo.gl/BvZRw) pour le plus grand bonheur de nous les dév android mais aussi des possesseurs de tel 
sous 2.3.x (rendez vous en mi 2014 pour déclarer la mort de Froyo, Gingerbread et HoneyComb :D ). 

Côté apps, la plupart des apps de **#google** vont recevoir des maj. Les plus attendus sont: 

* Hangout: Avec l’intégration des sms(l’ancienne app sms disparaît), le partage de position et d’autres nouvelles fonctionnalités. Si vous êtes pressés l’app est déjà dispo sur le net ==> [ICI](http://goo.gl/uuKh2U)
* Google Home: La home reçoit aussi une maj (je vais devoir désactiver [aviate](http://goo.gl/ApNyPM) :-( ) avec une nouvelle façon D’accéder à Google Now

Et bien d’autres nouveautés que vous retrouverez assez facilement sur le net…

##Android Studio 0.3.2

L’IDE pour faire des applications android est passé hier après-midi à la version 0.3.2 avec des fonctionnalités super sympa!

####Screenrecording

Quand on publie une app sur le play store, il faut montrer des screenshots qui exposent les fonctionnalités 
importantes de l’app. Encore mieux une vidéo de démo permet de voir comment l’app doit être utilisé. 
Cette vidéo peut donner envie ou pas d’installer l’app. Faire des vidéos de qualité n’était pas chose simple 
sur android. Mais ca c’était avant! le SDK android inclut un outil qui permet de faire des vidéos de démo à partir de notre tel et avec du MP4 comme ouput. C’est vraiment super! 
NB: _Il faut etre sous **kitkat**_

####Java 7

Android supporte maintenant java 7! Un dév android c’est avant tout un dév java! et plus tu es bon en java plus tu l’es en dév android! Donc avec java 7 (je rentrerai pas dans les details) on peut par exemple faire un switch case avec des strings! Du multi catch:

![Multi catch](/assets/multicatch1.png)

![Multi catch](/assets/multicatch2.png)

####Gradle integration

Si vous l’utilisez déjà comme IDE principal(je ne me rappelle même plus la dernière fois que j’ai ouvert eclipse…), 
vous vous êtes rendu compte de la lenteur quand on fait un build! Une petite astuce permet d’améliorer ca en 
utilisant le mode daemon de gradle. Maintenant avec studio une simple case à cocher permet de gagner en temps lors de vos builds! 

![daemon gradle](/assets/faster-builds.png) 

Voila voila en gros ce qui c’est passé hier! J’ai juste survolé mais je reviendrais sur les nombreuses nouveautés de l’API 19!