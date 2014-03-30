---
layout: post
title: Prise en main rapide de git
---

Lorsqu’on développe une application, en entreprise comme en solo, il est conseillé ou même essentiel 
d’avoir de bonnes pratiques. L’une d’elle est l’utilisation d’un gestionnaire de version pour avoir 
une certaine traçabilité de nos changements. Donc ici, on parla plus particulièrement de git qui est très célèbre 
dans le monde le l’open source avec des implémentations en ligne comme **github**, **bitbucket**. 

##C’est quoi un gestionnaire de version

Ce n’est pas bien compliquer; c’est juste un logiciel qui va te permettre d’avoir l’historique de toutes 
les modifications que tu as eu à apporter dans ton code ou tout simplement à un document. Et surtout de pouvoir 
retourner sur une version antérieur d’un document sans grande difficulté. 

Très sympa comme concept! 
Imagines ton boss te dit: "Demain on doit présenter la version stable du produit à un nouveau client". 
Alors que entre temps, tu étais en pleine modification du code et donc qu’il sera presque impossible de revenir 
à cette version stable vu que dés le départ tu n’as pas utiliser un système de versionning ou, comme 
certains le font, tu n’as pas créer un dossier __produitStableBackup__!
Donc on voit clairement, que ce système peut nous éviter bien des soucis :-). 
Il existe principalement deux modes de gestion de version:

* Le mode centralisé ou client-serveur: dans ce mode, le code source est sur un serveur. 
Tous les développeurs interagissent avec ce serveur soit pour récupérer les dernières mise à jour, 
soit pour apporter une nouvelle mise à jour. On comprendra qu’il est impératif d’être connecté au réseau local 
ou à internet pour que cette interaction puisse se faire. Exemple:Subversion(svn), cvs pour Concurrent versions system. 
* Le mode décentralisé: ici on peut dire que tout le monde est client et serveur. 
Et on a la possibilité d’interagir avec toute l’équipe sans passer par un point central. Tu peux donc 
travailler en n’étant pas connecté et faire des mises à jour en local; puis rendre ces mises à jour disponible 
dès qu’elles sont prêtes. Cependant même dans ce mode, il existera un point qui jouera uniquement le rôle de 
serveur mais ne sera pas indispensable pour travailler. Exemple: git, mercurial, bazaar. 

Vous l’avez compris, il s’agira ici de parler de git!

##Get started here

Il faudrait déja installer la bête. Rien de plus simple: `sudo apt-get install git-core` ou 
`sudo yum install git-core` et on peut enfin s’amuser ;-). 

Bon avant de s’amuser il faut d’abord se présenter(c’est le minimum non?): 

* Comment tu t’appelles? `git config –global user.name "Souleymane Sidibe"`
* Ton mail pour te joindre stp: `git config –global user.mail "souley@sidi.be"`

Il faut bien qu’on puisse savoir qui a fait tel modification ou comment le joindre. 

Maintenant que tout est prêt, il nous manque une chose. Un dépôt git. 
S’il existe déjà, il faut le récupérer; on parle de cloner un dépot. Et c’est assez facile à faire: 
`git clone http://chemin/vers/depot/killerApp.git [LeNomDuDepotEnLocal]` 

Si on ne met pas le nom, git va créer un dossier "killerApp". 
Petite précision, en plus du protocole http, il est possible d’avoir accès à un dépôt git via le protocole ssh 
(étant plus sécurisé), le protocole git ou avec un bête chemin du genre `/home/lead/projects/killerApp.git`.  
Si le dépôt n’existe pas en l’initialise.

{% highlight bash %} 
cd /chemin/vers/code/source
git init #pour initialiser un nouveau dépôt.
git add fichier.java #pour dire à git de suivre le fichier fichier.java
git add . #si on veut ajouter tous les fichiers du nouveau dépôt (même ceux des sous dossiers).
{% endhighlight %} 

Il y a souvent des fichiers qui ne doivent pas être suivis. c’est le cas des fichiers .class, certains 
fichiers de configuration. Pour ne pas que le système nous répéte après chaque `git st`(statut) 
qu’il y a des fichiers non suivis, il faut juste ajouter un fichier .gitignore et y mettre les noms 
des fichiers et dossiers correspondants. Gitignore comprend aussi les caractères spéciaux 
comme *.class (tous les fichiers .class).

[Ici](https://github.com/github/gitignore) un ensemble de prototypes de fichier .gitignore en fonction du langage ou de l’IDE. À personnaliser of course.
Une commande que j’utilise beaucoup et qui permet de voir l’état du dépôt: `git status`.
Elle permet d’avoir une vision global et fourni des informations sur la branche actuelle, les fichiers qui on été modifiés, 
les fichiers présents dans le dépôt et qui ne sont pas suivis. À propos des fichiers, on peut compter quatre statuts possibles: 

- **untracked** = >le fichier n’est pas suivi, faire un git add pour qu’il le soit 
- **unmodified** => le fichier n’est pas modifié; git rm pour retourner à l’état untracked 
- **modified** => le fichier a été modifié; il s’agit d’un fichier unmodified qui a été édité 
- **staged** => le fichier est indexés, donc il fera parti du prochain commit et retournera à l’état unmodified. 

Un défaut/qualité de git, s’est le grand nombre de commandes et d’options disponibles. Mais on est aidé par 
la complétion des commandes et aussi par la commande git help qui explique un peu ce qu’on peut faire et 
`git help <option>` pour avoir un man complet de la commande en question. 

Apres avoir implémenter la fonction qui va te rendre riche, il faut la "committer". En terme technique, 
c’est le fait de faire passer les fichiers qui étaient "staged" en unmodified. En plus simple, c’est juste 
le fait d’ajouter une entrée dans l’historique des modifications du dépôt. En général, il est accompagné 
d’un commentaire pour expliquer ce qui a été ajouter, modifier, supprimer: `git commit -m "commentaires"`.
On peut aussi faire en une seule fois un git add et un git commit: `git commit -a -m "coms"`.

##L’utilisation des branches

Retournons voir le développeur qui voulait modifier son code tout en ayant une version stable utilisable à tout moment.
Il a le choix entre plusieurs possibilités: 

* Créer un dossier avec des noms bizarres que lui seul peut comprendre (project_old_bon_2012_02_14_back): ici on prend l
e risque de se perdre dans sa forêt de dossiers ou de ne plus savoir lequel a ou n’a pas telle fonctionnalité
* Créer un clone du projet avec un git clone /chemin/project.git: ça peut être une alternative sauf que pour un gros 
projet, le disque dur peut crier!
* Ou créer une branche! git branch new_feature: c’est la meilleur des manières si on veut tester de nouvelles 
fonctionnalités tout en gardant une copie propre du projet.

Il faut savoir que quand on initialise un projet(`git init`) ou qu’on clone un dépôt (`git clone`). on est par  
défaut sur la branche "master". 
`git branch` permet d’avoir la liste des branches locales 
`git branch <nom_de_la_nouvelle_branche>`  pour créer une branche; en général je donne le nom de la feature 
que je voudrai implémenter ou la correction d’un bug. 
`git branche -d <nom_branche>` supprime la branche. 

Et bien d’autres options que tu peux explorer en faisant `git help branch`.

Oui mais bon c’est bien de créer des branches de les supprimer et tout mais comment on fait pour passer 
d’une branche à l’autre?! 
Et bin il suffit de faire git checkout <nom_branch> et on peut se permettre de faire un 
`git rm .` (supprimer tout le contenu du dossier) 
`git commit -m "tout supprimer!"` sans aucune crainte! 
Sur la branch master c’est comme s’il ne s’était rien passé (`git checkout master` pour vérifier). 
C’est cela la magie de git. Et il le fait avec une simplicité. On n’aura plus peur d’apporter de grosses 
modifications à des projets critiques ;-). 

A ce stade, tu dois pouvoir travailler avec git. Tu sais récupérer un dépôt ou en initialiser un, ajouter de
s fichiers, commiter, créer des branches, c’est le minimum à connaitre!
Dans un prochain article, on ira plus en profondeur dans l’utilisation de git.

