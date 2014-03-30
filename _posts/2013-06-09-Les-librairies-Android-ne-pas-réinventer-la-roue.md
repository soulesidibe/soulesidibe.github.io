---
layout: post
title: Les librairies Android:ne pas réinventer la roue.
---

Quand on développe pour une plateforme donnée, si on est novice ou pas assez informé, 
on a tendance à perdre du temps à implémenter des fonctionnalités qui, avec un peu de recherche 
sur la toile, nous aurait permis d’aller plus vite dans notre développement tout en ayant 
du code propre et maintenable. 
Le développement avec Android ne déroge pas à la règle. On trouvera une quantité inestimable 
de ressources sur des sites, forums et blogs qui nous facilite ou disons nous permette d’aller 
plus vite dans notre dév ou d’être plus focused sur le coeur même de l’application. 

Comme on peut le comprendre en lisant le titre de l’article, il s’agira ici d’essayer d’énumérer des 
librairies android qui nous font gagner en temps et en qualité pour notre application! Des librairies 
qui améliorent l’expériences utilisateurs tout en suivant les bonnes pratiques de la team Android de Google.

##Pull To Refresh

Dans une application Android, il est presque impossible de ne pas trouver de liste d’éléments 
(contacts, tweets, sms ou autres informations). Et en général les éléments de cette liste ne sont 
pas statiques. D’où la nécessite de proposer un mécanisme de mise à jour des données assez simple et 
intuitif: le PullToRefresh! 

![PullToRefresh](/assets/header_graphic.png)

on le voit sur l’image, d’un simple tiret vers le bas de la liste puis d’un relachement, on a la possibilité 
de mettre à jour les données! On peut imaginer appeler un service sur un autre thread pour récupérer les mises 
à jours. Une belle librairie open source qui permet de mettre en place un système de rafraîchissement que l’on 
retrouve dans presque toutes les apps connues. 

##Action Bar Sherlock

![ActionBarSherlock](/assets/actionbar.png)

La prise en compte des tablettes à partir des version 3.0 d’Android est venu avec son lot de nouveautés dont 
la action bar qui a rendu obseléte le bouton menu de nos anciens smartphones. 
Le problème qui se pose c’est que pour utiliser cette bar d’action dans nos apps on est obligé d’avoir 
comme minSdk l’api 11 dans notre manifest android! Ce qu’on ne peut pas faire pour cause une bonne partie 
des devices sur android est en dessous de l’api 11! 
La solution à cela est d’utiliser la librairie ActionBarSherlock qui va nous permettre d’avoir notre bar d’action 
quelle que soit la version d’android (au minimum api 7). Et ainsi offrir la même expérience utilisateur sans tenir 
compte de la version d’android de l’utilisateur. 

##Holo Everywhere

En suivant l’actualité autour de la sortie de la version 4 d’android, on n’a pas pu échapé au mot "Holo". 
C’est quoi Holo? 
C’est tout simplement le nom donnée au thème qui est apparu avec ICS et surtout il a permis de rendre Android 
beaucoup plus beau si on le compare un peu à IOS. Bon revenons au développeur qui veux faire une app qui le 
rendra riche! Bah le truc qui coince ici c’est la même application n’a pas le même rendu quand on la lance 
sur du Android 2.3.x ou sur du 4.2.x. Les composants ne sont pas les mêmes, la police de caractères change 
et bien d’autres aspects visuels changent. 
Vous l’avez compris, la librairie android Holo Everywhere, comme son nom l’indique va nous permettre d’avoir 
le thème holo partout; les devices avec ICS inclus! 

##Sliding Menu

![SlidingMenu](/assets/4png.png)

Si vous avez un android phone(normalement oui en tant que développeur android), vous avez du remarqué une 
nouvelle tendance dans les interfaces. En effet, certaines fonctions de l’application ne sont plus présentent 
sur la bar d’actions(qui sert le plus souvent pour partager du contenu sur les réseaux sociaux ou de menu 
contextuel quand on fait un long press sur un élément d’une liste par exemple) mais sur un autre menu caché à 
droite(et/ou à gauche) de l’écran et qui est rendu visible en faisant un "slid" des bordures de l’écran vers 
l’écran lui même. ICI une app de démo. On retrouve cette façon de faire dans la plus part des apps stars du 
playstore comme Google+, Evernote, The Verge, etc. 
Une très belle libraire qui permet d’avoir accès à certaines fonctionnalités de nos apps de façon très simple. 

##Et beaucoup d’autres!

La liste de librairies présentées est loin d’être exhaustive! Il y en a beaucoup d’autres à découvrir, 
tester et pourquoi pas utiliser dans nos apps. Un petit tour vers ce site, TheUltimateAndroidLibrary, 
nous permet de voir qu’il y a des dév qui ont déjà réglé certains soucis donc il ne faut pas se gêner!  
C’est le monde merveilleux d’android. Plein de librairies open source qui ne demandent qu’à être utilisées, 
forkées et améliorées. 


##Bonus 

Pour pouvoir utiliser ces librairies il faut les télécharger et les inclure à nos projets(sauf si on utilise 
le plugin maven). Chose pas tout le temps facile avec nos soucis de connexion internet en Afrique, ou Eclipse 
(avec ca dernière version) qui bug. Au bout d’un moment, on risque d’abandonner. 
J’ai la solution qui vous permettra d’avoir la config prête en un clin d’oeil :D 
Android Kick Start est un site qui nous permet de générer un nouveau projet android et de sélectionner en même 
temps des librairies qui seront incluses et configurées comme il le faut. Il suffira tout simple de télécharger 
le projet et de l’importer dans votre ide! Simple non ;-). 
N’ayez pas peur si on vous propose des noms de librairies que vous ne connaissez pas encore. 
J’en parlerai prochainement…