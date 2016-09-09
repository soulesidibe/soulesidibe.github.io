---
layout: post
title: Ma liste d’outils de dev
---

Au quotidien je travaille principalement sur du mobile et du backend. Et pour mener à bien les projets, j’ai une panoplie d’outils que j’utilise au quotidien. Donc l’idée est de partager ces outils avec vous.
Avant de commencer, j'utilise un Macbook Pro retina mid-2012 avec 16 Go de Ram, Intel Core i7 2.6ghz, SSD 500Go. Donc évidement je suis sous OS X El Captain.
Je ne présenterai que des applications mac(yes mac is best). La majorité d’entre elles ont un équivalent Windows et/ou Linux.


###IDE

**[Android Studio](http://developer.android.com/tools/studio/index.html)**: L’IDE préconisé par Google est excellent pour le developpement (j'ai connu eclipse + plugin ADT donc je sais de quoi je parle). Il est basé sur IntellIJ IDEA de la boite JetBrains.

**[IntellIJ IDEA](https://www.jetbrains.com/idea/)**: C’est le second IDE le plus populaire après Eclipse. Il est assez complet comme tous les autres IDEs d’ailleurs. Pourquoi celui-ci? On a énormément de docs sur leur site et sur Youtube. Le plugin scala est bien intégré ce qui permet de pouvoir developer une app **[Play!](http://playframework.com/)** sans trop  se prendre la tête. Côté Java pur, il a un outil de refractoring et de generation de code super puissant.

###Versionning

**[GIT](https://git-scm.com/)** : git init. C’est la commande que je tape tout juste après la creation d’un nouveau projet. Yup je versionne systématiquement mon code source avec git aui est un système de versionning très [puissant](https://twitter.com/soulesidibe/status/630097264560709632) que vous devriez essayer si ce n’est pas deja fait.  

**[SourceTree](https://www.atlassian.com/software/sourcetree/overview)** est un outil de chez Atlasssian qui propose un client Git et Mercurial très complet. Il a une interface assez simple qui permet de faire des recherches assez poussées dans l’historique, et évidement de lancer toutes les actions que permet Git. Cependant, j’ai plus le réflexe d’utiliser la ligne de commande pour des taches simples comme le commit, checkout, etc. Sourcetree me sert plus à avoir une meilleur visualisation de l'historiaque du repo.


###Mes éditeurs de text  

**[Sublime Text](https://www.sublimetext.com/)** est pour moi le meilleur éditeur de text du moment. Les fonctionnalités que j’utilise le plus sont la recherche de text dans un fichier ou un dossier et le fait de ne pas avoir a enregister un fichier même après fermeture de l’éditeur. Il est aussi extrêmement personnalisable avec un system de plugin.  
**[Vim](http://stackoverflow.com/questions/21012203/how-can-i-install-macvim-on-os-x)** est l’un des premiers outils que je cherche à installer sur mon poste ou sur un serveur.  Quand on prend le temps de l’étudier, on se rend compte qu’il est tout aussi puissant.

Atom de chez Github semble prometteur mais je n'ai pas eu le temps de l'utiliser.

###Terminal

**[Iterm](https://www.iterm2.com/)** sans aucun doute le meilleur terminal sous mac OS. L'une des features que j'aime bien c'est le fait de pouvoir diviser l'ecran en un nombre illimité de terminal. L'outil permet aussi une personnalisation assez poussée. 
Mention spéciale: Terminator(Linux).

###Productivity

**[Evernote](https://evernote.com/)** est une app de prise de note qui permet aussi de faire de la collecte d'informations depuis une page web, un pdf, une video youtube, etc. Je l'utilise depuis quelques années et je vous le conseillle vivement. 

**[Amphetamine](https://www.macupdate.com/app/mac/52683/amphetamine)** est un petit programme qui empêche votre mac d'aller en veille. Le plus connu est caffeine. Mais celui ci offre plus de fonctionnalite comme le durée de la non mise en veille.

**[lastpass](https://lastpass.com/)** est un gestionnaire de mot de passe. Il permet aussi de générer des mots de passe robustes et vous aides à les utiliser avec son outil auto form filler dispo sur tous les navigateurs et aussi sur mobile. J'y garde les mots de passe de mes comptes persos mais aussi mais accès à certains serveurs ou base de données. Il permet aussi de partager des mot de passe entre utilisateurs lastpass. 

###Autres

**[TunnelBlick](https://tunnelblick.net/)** est un client VPN gratuit et open source. Si dans votre structure vous avez besoin de vpn pour avoir acces a vos serveur distant TunnelBlick fera l'affaire.

**[filezilla](https://filezilla-project.org/)** est un client(ou serveur) FTP, SFTP avec une interface graphique assez simple où avec un drag n drop on peut envoyer ou récupérer des fichiers distants. 

**[docker](https://www.docker.com/)** est une platforme open source pour les développeurs et les sysadmins permettant de simplifier le build, le deploiement et la mise en production d'application sur son laptop, sur un datacenter ou sur le cloud. Docker me permet de ne plus avoir à installer certains outils directement sur mon mac. Si j'ai besoin d'une base Mysql je lance un container Mysql. That's it! Tout est transparent pour l'os. J'ai juste à avoir docker sur ma machine et il sait comment faire tourner un container peu importe son contenu. Dans mon exemple c'est un serveur mysql mais ca peut être n'importe quoi! Un serveur apache2, tomcat, java 9, etc. Je reviendrai sur Docker.

**[kitematic](https://kitematic.com/)** est une interface graphique au dessus de docker pour ceux qui n'aiment pas trop la ligne de commande. Donc avec quelques clics, on arrive à récupérer une image docker et lancer un container assez facilement. 

**[virtualbox](https://www.virtualbox.org)** est un outil de virtualisation gratuit et multiplatforme. Avec docker, j'ai de moins en moins le besoin d'avoir une machine virtuelle. 

**[androidTool](https://github.com/mortenjust/androidtool-mac)** permet de prendre des videos ou des screenshots de son telephone android. Il permet aussi d'installer un apk directement sur un device. 

**[Deezer](http://www.deezer.com/)** j'aime écouter de la musique pour me concentrer sur ce que je fais et ne pas entendre ce qui se passe autour de moi. Deezer est une assez bonne application offrant des millions de titres avec son abonnement mensuel de 3500FCFA. Si vous avez un abonnement deezer je vous propose cette **[playlist](http://www.deezer.com/playlist/1036831501?utm_source=deezer&utm_content=playlist-1036831501&utm_term=311696685_1471868343&utm_medium=web)** à écouter pendant que vous êtes focus sur une tâche importante :)


Voila un peu quelques apps que j'utilise très souvent. La liste n'est pas exhaustive mais j'espere que certaines d'entre elles vous serviront et vous permettront d'être plus productif.  
Peace!
