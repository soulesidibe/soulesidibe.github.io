---
layout: post
title: Tester son application android
---

Tester! Je vous l'accorde ce n'est pas la tache la plus fun quand on développe son appli(web, mobile, ou autre). Mais on est d'accord qu'il est indispensable de tester son app.  
Le but de cette article n'est pas de convaincre de tester. Il sera orienté sur comment tester en utilisant *gradle* et *Android Studio*  et surtout quels sont les outils disponible dans le monde android pour faciliter cette parti important dans la phase de devolepment de votre app.

##Qu'es ce qu'on peut tester?
On peut tout tester dans une app android. Je vais essayer de lister ce qu'il est possible de faire: 

* __Le UI__: on teste ici les interfaces graphiques de nos activités ou fragments. par exemple vérifier la présence des composants graphiques et de leurs états(EditText avec tel id est-il présent, quelle est ca valeur, son hint, etc ) 
* __Les composant de l'app__: j'entends par la, les Services, les ContentProviders, les BroadcastReceivers
* __Les POJOs__: toutes ces classes que nous créons pour mettre en place la logique de notre app
* __Les interactions dans l'app__: tester les fonctionnalités de l'app, les use cases 

La liste n'est pas exhaustive. On peut juste retenir qu'il est possible de mettre en place des tests pour tous les composants de notre application.

##Tester avec Android  Studio et le build system gradle
Tester son application en utilisant le systeme de build gradle est disons tres facile a prendre en main quand on respecte les conventions de l'outil.