---
layout: post
title: Git:La suite
---

Dans un précédent article je vous parlais de git et d’une certaine façon de bien démarrer avec cette outil très puissant 
et surtout incontournable en ce moment.Maintenant qu’on arrive à travailler avec git on peut essayer de voir un peu plus 
ce qu’offre la bête. 

##Merge, push, pull

Imaginons que vous avez créez une nouvelle branche "newFeature" histoire de tester une nouvelle fonction que vous 
trouvez pertinente. 
Vous codez, vous commitez. Tout marche! vous venez de mettre en place une fonctionnalité qui va vous valoir 
une prime! Bah il ne reste plus qu’à faire un push! 
Oops non mais attend je suis sur une autre branche; ai-je le droit de faire qu’en même le push? 
Heuu oui et non. 

* Non du fait que, au niveau du dépôt distant, il n’y a pas de banche "_newFeature_". Donc si on fait un `git push origin master` 
on aura comme message "Everything up-to-date". Ce qui est normal si on comprend un peu comment marche les branches. 
On demande à git de mettre à jour le dépôt distant identifié par "origin" avec nos modifications locales de la 
branche master(`git push origin master`). Alors que nos modifications ont été faites sur la branche "newFeature". 
Pour y remédier: il faut informer la branche master qu’il s’est passé des trucs et qu’elle doit se mettre au parfum: on merge! 
C’est très simple à faire: 
{% highlight bash %}
git co master
git merge newFeature
{% endhighlight %} 
D’abord on se positionne sur la branche master (co= checkout, je ferai un article sur les raccourcis git et comment en rajouter) 
puis on fait un merge en donnant en paramètre le nom de la branche que l’on doit merger (l’auto-complétion marche pour les branches). 
Si on est seul à travailler sur ce dépôt local tout devrait bien se passer. Mais si ce n’est pas le cas et que un autre 
développeur a modifié un des fichiers sur lesquels vous avez apporté des modifications, il peut y avoir des conflits! 
Pour rappel, git ne gère pas les fichiers mais le contenu des fichiers. Donc en cas de conflit, git vous guidera à régler le conflit. 

* Oui Dans le cas où newFeature est une branche de suivi.Une branche de suivi est une branche locale liée à une branche 
située au niveau du serveur. 

Pour récupérer une branche distante: 

{% highlight bash %}
git co --track origin/newFeature
{% endhighlight %} 
La on aura une nouvelle branche newFeature et on sera en même temps connecter deçu. 
Avant de pousser sur la branche distante il faut toujours récupérer les derniers changements au cas où il y en a; 
puis faire son push:

{% highlight bash %}
git pull
git push
{% endhighlight %}
Lors du pull, il est possible que git vous sort votre éditeur par défaut(vim, gedit, ou autre). C’est dans le cas où la branche 
de suivi et la branche distante diverge(en plus simple, ne sont plus identiques). Donc git fait un merge des deux. 
Il est malin ce git! 

Mais au fait comment on crée une nouvelle branche sur le dépôt distant? 
Il suffit de pousser la branche! 

{% highlight bash %}
git branch nouvelleBranche
git push origin nouvelleBranche
{% endhighlight %} 
De même si on veut en supprimer une:

{% highlight bash %}
git push origin :nouvelleBranche
{% endhighlight %}
Le ":" suffit pour supprimer la branche distante! Mais elle sera toujours présente en local.

##Git Stash

Il arrive qu’on soit entrain de coder un truc dans une branche et que à un moment on ait besoin de changer de branche 
pour corriger un bug ou un truc du genre. Vu que git n’aime pas quand on essaye de faire un checkout alors que la branche 
actuelle a subit des modifications et que on ne veux pas commiter du code non fini, la solution ici est de dire à git de 
garder les modifications dans un coin et qu’on reviendra continuer notre travail plus tard: `git stash`.

Si on fait un `git status`, on remarque que le répertoire de travail est propre! les modifications ont été mises à l’écart. 
Et si à un moment on souhaite revenir sur notre sauvegarde: `git stash apply` ou `git stash pop`.

Et la on se rend compte que tout est revenu comme on l’avait laissé! C’est génial. 
La différence entre git stash pop et git stash apply est que avec pop, le stash est appliqué et supprimé mais 
avec apply il est juste appliqué; donc on peut toujours le retrouver et l’appliquer à nouveau. 
Au passage pour supprimer un stash, `git stash drop [nomStash]`. 
Pour éviter de faire un `git add` apres un git stash apply, rajoutez `–index` à la fin: `git stash apply --index`. 

Il est possible qu’on est mis, de la même manière, en quarantaine d’autres modifications. 
Pour voir la liste des stashs, il suffit de faire `git stash list`. Et git nous affiche des lignes de ce genre: 
`stash@{0}: WIP on master: 25af4b3 autre commit` 

Et là, on a le nom du stash (_stash@{0}_), le nom de la branche(ici _master_) et des informations sur le commit qui 
précède ce stash (_25af4b3_ autre commit). 
Avec le nom du stash, on peut appliquer un stash spécifique en faisant `git stash apply [nomStash]`. 
Si on ne met pas de nom, le dernier stash sera utilisé. 
Et un petit dernier, Si un stash devient important et que vous sentez que c’est plus qu’une petite modification, 
vous pouvez la transformer en une branche! 
`git stash branch nomBranche nomStash` 
Et git vous connecte automatiquement à cette nouvelle branche! 
Git stash est une commande très puissante avec laquelle on peut faire énormément de choses. 
Je vous conseille d’aller lire le man (man git-stash ou git help stash)  pour voir les autres possibilités. 
D’ailleurs, je vous conseille d’aller consulter le man pour toutes les autres fonctionnalités de git. On y rencontre 
des astuces qu’on ne voit pas souvent dans les tutos. 

Vous le savez git est vraiment puissant. Il permet de ne plus perdre des heures à chercher des fichiers ou des dossiers. 
Et ainsi se concentrer sur l’essentiel! 
Mais git peut aussi être fun. J’entends par là, personnaliser les commandes, la coloration de git et bien d’autres 
qui feront l’objet d’un article! :D 

