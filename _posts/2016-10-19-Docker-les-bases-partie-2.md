---
layout: post
title: Docker, Les bases (2e partie)
---

[Lors de la première partie](http://soulesidibe.com/2016/09/26/Docker-les-bases/), on a fait une présentation de docker. On a introduit quelques notions de base indispensables pour avancer sur le sujet.  
Cette partie sera un peu plus pratique :)

## Installation
Les équipes de docker ont fait un travail énorme pour faciliter la vie aux utilisateurs. Je me rappelle encore quand il fallait passer par virtualbox pour utiliser docker sur mac ou windows...  
Docker est disponible sur [linux](https://docs.docker.com/engine/installation/linux/ubuntulinux/), [mac](https://docs.docker.com/engine/installation/mac/) et [windows](https://docs.docker.com/engine/installation/windows/). Pour le cas des distributions linux, la manip n'est pas forcement la même. Vous aurez la marche à suivre pour toutes les distros supportées sur [le site](https://docs.docker.com/engine/installation/linux/ubuntulinux/).  

#### Docker for mac

Pour ce post, je vais parler de l'installation sous mac. Sorry pour les autres.  
Déjà, il faut récupérer [le fichier dmg](https://docs.docker.com/docker-for-mac/). On a la version stable et la version beta dispo. Pour le reste, je n'ai rien à vous expliquer. Un user mac sait quoi faire d'un fichier .dmg.  
Après l'installation, l'app sera disponible au niveau du launchpad:  
![docker](/assets/docker_launchpad.png)  
Comme je vous l'expliquais, cette install de docker vous donne accès au daemon qui sera visible au niveau du menu bar de mac:
![docker menu bar](/assets/docker_up_and_running.png)  
On a aussi accès à la commande `docker` depuis le terminal.  

![docker_cmd](/assets/docker_version_cmd.png)

## Lancer son premier container

La facon la plus simple de lancer un container: `docker run hello-world`.
Alors décomposons un peu cette commande:  

![hello world docker](/assets/hello_world_docker.png)

* `docker` : c'est le client qui permet d'interagir avec docker    
* `run` : c'est l'option qui permet de lancer un container. Nous verront les autres options de `run` à connaitre  
* `hello-world` : C'est le nom de l'image qui sera utilisée pour lancer le container.  

C'est cool tout ca mais pour juste afficher un message sur le terminal, il y a plus simple que docker. 
On va essayer de lancer un container beaucoup plus intéressant. Disons que j'ai besoin d'un serveur web(apache2) sur une machine en prod sous ubuntu 14.04. Et que j'aimerai tester la config sur ma machine avant de le faire en prod. J'ai plusieurs options. Soit j'installe une solution de virtualisation comme virtualbox, soit j'utilise docker. 
D'abord j'ai besoin d'une image de Ubuntu 14.04. Ce qui me permet d'introduire l'option `search`:  

![docker search](/assets/docker_search_ubuntu.png)  

La on voit qu'il existe une image Ubuntu dispo sur hub.docker.com. Le registry officiel de docker. Notre serveur web doit tourner sur une version particulière de Ubuntu la LTS 14.04. La commande search ne permettant pas de rechercher par tag, nous allons devoir essayer de récupérer cette version  14.04 par la même vérifier son existance.  

![docker pull](/assets/docker_pull_ubuntu.png)  

L'option `pull` nous permet de récupérer une image depuis un registry. J'ai aussi spécifié la version de Ubuntu que je voulais via ce qu'on appelle les **tags**. C'est un peu l'equivalent des tags avec **git**. Ca nous permet de "snapshoter" les évolutions d'une image. En général, on a un numéro de version, un nom. On aurait pu mettre par exemple `docker pull ubuntu:trusty`. Trusty étant le nom de la version 14.04 de Ubuntu. C'est au créateur de l'image de choisir le tag qu'il veut attribuer à une imagine.  
Donc nous avons récupéré une image, on va vérifier au niveau de ma liste des images si elle est présente.  

![Docker images](/assets/docker_images.png)

Good! J'ai réussi à récupérer une image docker sur un registry. Now je suis prêt pour lancer mon container. 

![Docker images](/assets/docker_run.png)

La commande est simple. `docker run` pour dire que je veux lancer un container, `-ti` pour avoir un TTY et avoir accès au STDIN, `ubuntu:14.04` pour specifier l'image et le tag que je veux utiliser, `bash` pour lancer la commade bash au lancement du container. C'est le minimum dont on a besoin pour pouvoir installer notre serveur web apache2.  
Il y a énormement d'options sur **[docker run](https://docs.docker.com/engine/reference/commandline/run/)**. On en reparlera au fur et à mesure.  
So on voit bien sur le screenshot qu'on est plus sur mon mac mais bien sur une machine ubuntu 14.04. On pourra donc installer notre serveur apache2 et faire nos tests!  
On aurait pu faire directement un `docker run` sans passer par un `docker pull`. Quand l'image n'existe pas sur la machine, elle est automatiquement récupérée si elle existe sur le registry.  
Nous savons comment démarrer un container. Nous pouvons installer notre serveur apache comme sur une vrai machine ubuntu.  L'inconvénient de cette façon de faire est que à chaque lancement du container, il faudra installer à nouveau le serveur apache2. Docker nous donne donc la possibilité de creer des images qui auront l'état de base souhaité. Dans notre cas, un ubuntu 14.04 avec apache2 up and running.  

##Créer sa propre image  

Docker nous donne la possibilité de créer des images. Dans le cas de mon serveur WEB, au lieu de lancer à chaque fois un container Ubuntu et d'y installer notre serveur, on peut créer une image docker ayant les specs souhaitées. 

#### Enregistrer les modifications d'un container

On peut créer une nouvelle image à partir des modifications qui ont étè apportées sur un container déjà lancé. Via la command `commit`. 
D'abord, il faut connaitre l'ID du container.  

![liste container](/assets/docker_ps.png)  

La commande `docker ps` nous permet de voir la liste des containers qui sont actuellement démarrés avec différentes informations dont l'ID qui permet d'identifier un container. On a tout ce qu'il faut pour créer une image avec un commit des modifications du container actuel.  

![creation container](/assets/docker_commit.png)  

La aussi la team docker a fait très simple. `docker commit b187af19c708 soulesidibe/apache2:0.1`. Ce qui signifie, je veux créer une nouvelle image à partir du container ayant l'ID `b187af19c708`. L'image s'appellera `soulesidibe/apache2` et donne lui le tag `0.1`. Je fais un `docker images` pour vérifier la création effective de la nouvelle image. `docker images` suivi du nom de la nouvelle image permet de filter en fonction du nom.  
Now on peut lancer directement notre container sans avoir à installer à chaque fois le serveur apache. Pas mal non?  

![run new container](/assets/docker_run_new.png)  

#### Dockerfile  
Pour moi, c'est le meilleur moyen pour créer une nouvelle image parce que plus simple et surtout plus facile à partager dans une team par exemple.
Il faut d'abord créer un fichier et lui donner le nom `Dockerfile`. Par convention, on crée un dossier avec le nom de la futur image et on y met le Dockerfile.  
Voici le Dockerfile de notre serveur web apache2:  

![Dockerfile](/assets/apache2server_Dockerfile.png)  

Chaque ligne est une instruction expliquant comment créer mon image docker. Et surtout chaque instruction est une couche au niveau de l'image. Pour chaque instruction, nous avons un mot clé et des arguments. La liste compléte des instructions est dispo en [ligne](https://docs.docker.com/engine/reference/builder/) mais nous allons voir un peu ce que fait ce fichier Dockerfile:  

* `FROM ubuntu:14.04`: FROM permet de spécifier l'image de base utilisée pour créer notre nouvelle image. Dans notre cas, on part sur un Ubuntu avec le tag 14.04. Ca permet vraiment d'aller vite dans ce quon fait. Je vous conseille de toujours partir d'une image officielle. 

* `MAINTAINER Souleymane Sidibe<souleymane.sidibe01@gmail.com>`: Cette ligne permet juste de donner le contact de l'auteur de cette image. 	

* `RUN apt-get update && apt-get -y install apache2`: RUN nous permet d'executer des commandes dans l'image. Ici, nous avons juste besoin d'installer le serveur apache mais on peut imaginer toute sorte de commandes. Sky is the limit.

* `ENTRYPOINT ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]`: ENTRYPOINT permet de rendre le container executable en lancant votre instruction dès son lancement. C'est ce que nous faisons avec notre serveur web apache2. 

* `EXPOSE 80`: EXPOSE permet de dire à Docker que notre container écoute sur un port spécifique. Cela ne veut pas dire que le container est accessible depuis l'hôte. Pour ce faire, il faudra faire un mapping entre le port du container et un port sur la machine hôte avec l'option -p de la commande `docker run`.  

L'étape suivante est de construire l'image à l'aide de la commande `docker build`:  

![build image](/assets/build_image_docker.png)  

Comme on peut le voir, `docker build` c'est juste une succession de couches(instructions) rajoutées à l'image de base specifiée dans l'instruction FROM. Docker build a un fonctionnement assez intéressant. `Docker build` n'execute pas une instruction qui a déjà été execute si elle n'a pas changé ou si une instruction au dessus d'elle n'a pas changé. La couche correspondante étant dans son cache. Ce qui me permet de ne pas avoir besoin de internet pour faire une installation de apache2 sur mon ubuntu via la commande apt. Cool non :)  
Créer un Dockerfile est assez simple. Dépendant de ce que vous voulez faire. Avant de vous lancer dans la creation d'une image, vérifier si un equivalent n'existe pas déjà sur le [hub](hub.docker.com). Vous y trouverez surement une image qui vous fera gagner du temps.

Voila un peu pour cette partie! Nous avons installé docker sous mac. Puis nous avons lancé notre premier container et pour finir nous avons créer notre premiére image à partir d'un container et d'un dockerfile. C'est tout pour ce post. J'essaie de ne pas etre trop long.  
Lors de la partie 3, nous parlerons des volumes.
