---
layout: post
title: Docker, Les bases (1er partie)
---

Je suis sur que vous en avez entendu parler(un collègue, une mailing list, un talk, un article) et peut-être même que vous avez essayer mais bon voila ca s’est pas trop bien passé donc vous avez give up. Je suis la pour vous ramener dans le wave de la dockermania! Et vous montrer la puissance et la simplicité du truc surtout pour nous les développeurs mais aussi pour les ops!
 
    
     Docker is an open platform for developing, shipping, and running applications. Docker enables you to separate your applications from your infrastructure so you can deliver software quickly. With Docker, you can manage your infrastructure in the same ways you manage your applications. By taking advantage of Docker’s methodoligies for shipping, testing, and deploying code quickly, you can significantly reduce the delay between writing code and running it in production.

C'est la definition officielle disponible sur le site de docker. Je vais essayer de découper la définition et d'expliquer à ma manière:

* `Docker is an open platform for developing, shipping, and running applications` : Avec docker nous, développeurs, ne pourrons plus avoir l'excuse "Ca marche sur ma machine". Sorry guys... Docker nous permet de garder le même environnement en mode dev, staging et production. Votre application est encapsulée dans une boite appelée `container` qui sera la même sur votre machine, sur le serveur de test, de prod. 
* `Docker enables you to separate your applications from your infrastructure so you can deliver software quickly`: avec docker la structure de votre infra n'est plus aussi critique. Vous avez juste à y avoir un composant qui est capable de lancer un container, docker. Nous reviendrons sur cela. 
* `With Docker, you can manage your infrastructure in the same ways you manage your applications.`: docker étant "developer friendly", vous avez la possibilité de manipuler votre infra comme une application. Ceci parce que votre infra devient juste un ensemble de container à gérer. La notion de serveur devient abstraite. Nous verront que manipuler un container est d'une simplicité bluffante 
* `By taking advantage of Docker’s methodoligies for shipping, testing, and deploying code quickly, you can significantly reduce the delay between writing code and running it in production.`: Déployer une application en production dans certain cas(please automate!) n'est pas une tâche facile. Démarrer, arrêter, configurer un container en dev ou en production est une tâche relativement simple et en plus docker peut facilement être intégré dans le workflow de votre CI/CD. 

Avec docker, le fameux `Write once, run anywhere` prend tout son sens. Toute votre architecture sera contenue dans 1 ou plusieurs containers(en fonction que vous soyez en mode monolithique ou micro-services) et la machine hôte n'aura qu'à avoir docker installé et that's it! La gestion des containers, la communication entre containers tout est géré avec docker. 

##Terminologies  

![Docker Architecture](/assets/architecture_docker.png)

####Docker Daemon  

C'est le process qui permet de manipuler les containers. C'est un daemon, donc il tourne en background sur la machine hôte. À lui tout seul, il sert à rien. Pour l'utiliser il faut un client qui utilise l'api qu'il expose.  

####Docker client  

C'est le client par défaut qui permet d'interagir avec le Daemon docker. Il est accessible via la commande `docker` disponible après installation sur votre machine linux, mac ou windows. Il faut savoir que par défaut quand on installe docker, on a le daemon et le client. Mais à partir du client on peut manipuler un daemon distant aussi. On a aussi la possibilité d'utiliser directement l'API si le client ne nous suffit pas. 

####Docker images  

Une image docker est juste une description de ce que doit être le container. On peut dire que c'est un template. Par exemple une image peut contenir un systeme d'exploitation Ubuntu avec Java 8 et une appli java installés. Pour construire une image docker, on crée un fichier `Dockerfile` du même nom qui est un plain text et qui permet de décrire le contenu de notre image. On a aussi la possibilité de récupérer une image prête à l'usage. Les produits qu'on utilise pour le dev ont souvent une image officielle déjà prête. 

####Docker containers  

Un container est juste une instance executable d'une image docker. C'est ce container la qui sera executer pour faire tourner votre app, votre base de données. Vous pouvez le lancer, le supprimer, l'arrêter avec le client docker ou via l'API du daemon. La particularité d'un container docker est qu'il est isolé du monde extérieur par défaut. Sauf si nous lui donnons accès.  
Exemple, j'ai une app grails qui expose une API sur le port 9000. On a la possibilité de la rendre accessible sur un port spécifique de la machine hôte. Autre détail, toute modification faite dans le container est perdue après arrêt ou suppression du container. D'où des fonctions de persistence sur la machine hôte du container. 

####Docker registries  

Une docker registry est une librairie d'images docker. Cette librairie peut être sur la même machine ou en remote. Par défaut, docker dispose d'un registry. hub.docker.com. Quand on fait un `docker pull mysql`, on recupere l'image de mysql depuis le registry de docker sur notre machine. Et on peut donc lancer un container et avoir une base de données mysql up and running en quelques minutes. Un registry peut aussi être privé pour un usage en entreprise par exemple. On ne veut pas forcément rendre publique nos apps!

####Docker services  

Les dockers services vont permettre de créer un swarm. Docker swarm vient faciliter la gestion de containers distribués dans plusieurs Daemon docker situés sur plusieurs machines. Vous avez un swarm manager qui vous permet de gérer globalement votre infra! Vous aurez acces à tout ce qui se fait de mieux en terme de clustering, load balancing, sécurité, etc.


Avec ces quelques notions, on a une petite idée de ce qu'est docker et de ce qu'on doit pouvoir faire avec. C'est un outil super puissant qui peut changer la facon dont on travaille au quotidien mais aussi la facon dont on manage nos applis en production.
Pour ne pas etre long, on va sarreter la pour cette partie. Lors de la 2e partie on mettra les mains dans le cambouis!



