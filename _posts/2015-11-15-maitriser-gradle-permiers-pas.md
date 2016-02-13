---
layout: post
title: Maitriser gradle. Les premiers pas.
---

Compiler un programme Java(un HelloWorld) n'est pas compliqué. On peut le faire à la main avec la commande javac. Maintenant, que faire dans un projet avec les contraintes suivantes: 

* le projet a besoin d'autres ressources pour marcher à part le code source. Par exemple des fichiers images, json, xml, etc.
* le projet doit lancer un jeu de tester JUnit avant chaque génération du Jar du projet
* le projet a une multitude de dépendances pour fonctionnener(ex: guava, junit4, jodatime) et télécharger les fichiers jar depuis leurs sites respectifs n'est pas une solution vu qu'il faut une intervention humaine
* Le projet doit générer une documentation 
* Le fichier jar généré doit être déposé sur un repository

On peut bien créer notre fichier __shell__ pour gérer tout cela mais ce fichier devra aussi être maintenu autant que le code source du projet.
Quand est-il d'un projet android? Le processus de build est très complexe surtout quand on commence à s'ammuser avec les variantes. 

![android build](/assets/build_android.png)

Donc on voit que le build d'une app android ne peut pas se faire à la main ou avec un script shell. Nous avons besoin d'un systeme beaucoup plus robuste. Il s'agit de __**Gradle**__.

##Gradle un outil de build (build tool)
Gradle est un outil de build récent. Avant lui on avait le populair maven qui est bien connu dans la communaute java et qui se base sur un fichier pom ou est decrit comment on veut que notre projet soit construit et ou on déclare aussi nos dépendances. 
 Gradle comme la plus part des outils de build nous garantit trois points essentiels: 
 
 * Portability: Un outil de build doit pouvoir faire son job indifféremment de l'environnement de build.
 * Automation: une simple commande doit pouvoir lancer tout le process de build et rendre invisible toute la complexité.
 * Repeatability: le resultat du build doit etre le même no matter who runs it.

 Il est devenu indispensable d'avoir ce genre d'outil pour simplifier le travail des developpeurs android. On doit pouvoir se concentrer sur l'essentiel. Ecrire du code. Du bon code. Et ne pas trop se casser la tête sur comment les versions free et paid de mon app ont étè générées. Google en ayant choisit gradle et intellij nous aide dans cela. 
 
## Installer gradle

Il n'est pas necessaire d'installer soit même gradle. Gradle lui meme s'occupe de sa propre installation sur la machine. Bizarre non?  
En fait dans un projet gradle nous avons ce qu'on appelle le wrapper. Le wrapper est consitué d'un script shell que l'on va utiliser pour lancer les commandes de gradle et un dossier contenant un fichier jar tres léger et un fichier properties:

![Gradle Wrapper](/assets/gradle_wrapper.png)

`build.gradle` c'est le fichier de build gradle, on pourra y définir nos tasks, configurer notre projet. `gradlew` et `gradlew.bat` respectivement pour linux/mac et windows permettent de lancer les commandes gradle. Si la version de gradle specifier dans le fichier `gradle-wrapper.properties` n'est pas présente sur la machine, elle sera installée. 
De ce fait un build sera a priori le même sur toutes les machines, et surtout sur le serveur d'intégration continue.  
On peut cependant vouloir installer manuellement une version spécifique de gradle sur notre machine. Et pour lancer une commande gradle, au lieu de faire un `./gradlew CMD`, on fera un `gradle CMD`. 

### Installer gradle sous mac

Déjà il faut s'assurer d'avoir java installé. Lancer `java -version` pour vérifier.  
Ensuite il y a plusieurs façons d'installer gradle:

* brew: `brew install gradle` est la facon la plus simple si vous êtes un utilisateur de `brew`
* Manuellement: il faudra télécharger le zip depuis le [site](https://gradle.org/downloads/), le désarchiver puis ajouter le chemin dans le PATH.

### Installer gradle sous windows
Idem que la deuxieme méthode utilisée sous mac.  

Pour veririfier que tout va bien: `gradle --version`

![Gradle version](/assets/gradle-version.png)

## Le fichier build.gradle

Voici un exemple de fichier `build.gradle` provenant d'un projet android: 

<<<<<<< HEAD
``` gradle
=======
>>>>>>> d323f4db034efb5a688aa9a1ff68d3638077fee9

	apply plugin: 'com.android.application'
	
	android {
   		compileSdkVersion 23
    	buildToolsVersion "23.0.1"

    	defaultConfig {
        	minSdkVersion 16
        	targetSdkVersion 23
    	}
	}
	
	task hello {
		doLast {
			println "Hello!"
		}
	}


Il a un format similaire à celui du `json`. En fait, cela est du au language groovy qui permet d'écrire du code qui est facile à lire et à comprendre.  
Mais surtout à cause de ce qu'on appelle la DSL (Domain Specific Language) qui est un langage au dessus de Groovy qui en fonction du plugin qui est appliqué nous donne accès à un ensemble de fonctionnalités qui nous permet de décrire notre build. Ici avec le plugin `com.android.application` on a accès au mots cle `android`, `task`. Pour un autre plugin on n'aura pas acccès aux mêmes fonctionnalités. 
Cette DSL n'est pas figée. Elle est extensible par le billet de plugin. Le fichier de build doit rester declaratif et ne doit pas être encombré de logique de code. Donc pour masquer cela, on peut créer un plugin avec notre propre DSL. C'est le cas avec le plugin Android.  
C'est ce qui fait que Gradle est polyglote. Il peut builder n'importe quelle projet peu importe le langage utilisé dans le code source. il suffit d'appliquer le bon plugin ou de le créer s'il n'existe pas.

## Les Tasks (ou taches)

Une tâche est un des elements de base de gradle. Elle permet d'executer des actions. Une action peut etre de lancer la commande `javac` pour compiler le source code java, de copier un fichier, d'écrire un simple "Hello World". A ce niveau c'est votre code groovy qui est executé.  
Une tâche peut aussi dépendre d'une autre tâche. On peut donc se brancher a n'importe quelle etape du cycle de build.  

####Creer une tache

Gradle nous offre différente façon de créer une tache dans notre fichier build.gralde:  

* `task("myTask") ou project.task("myTask")` est la façon la plus simple de le faire. Pour verifier, il faut lancer `gradle tasks` ou `./gradlew tasks`
* Grace à Groovy on peut aussi faire ` task "myTask"` vu que les parenthèses ne sont pas obligatoires quand on a qu'un seul paramètre
* Encore mieux Gradle comprend aussi `task myTask`. Ce qui n'est pas du code groovy valide vu que `myTask` n'a pas étè déclaré. Ceci est du a ce qu'ils appellent [AST Transformations](http://groovy-lang.org/metaprogramming.html#_compile_time_metaprogramming) qui permet à la compilation de générer du code ce qui permet de reduire le code inutile et de rendre encore plus comprehensible le fichier de build.
* la facon la plus répandue de créer une tache est la suivance :
	
		task myTask {
			//Donnons une description a notre tache
			description "My first task"
			
			//Un group
			group "Custom Tasks"
			
			//une action a faire en premier
			doFirst {
				//groovy code...
			}
			
			//une action a la fin
			doLast {
				//groovy code...
			}
		}
		

	Pour une tache simple, on peut utiliser une syntaxe reduit `task myTask << { println "Hello"}`. Et le closure sera executer en dernier comme un `doLast`
	
Pour executer une tache on utilise la commande `gradle` suivit du nom de la tache  
![execute task](/assets/gradle_exe_task.png)

#### Dependances et ordre d'execution des taches
  
  Gradle nous donne la possibilité de configurer les dépendances des tâches que nous créons. Entre elles ou par rapport aux tâches disponibles. La commande `dependsOn` nous permet de déclarer une ou plusieurs dépendances pour une tâche:
  
  
  task putOnSocks {
  	doLast {
   		println "Putting on Socks."
   	}
  }

  task putOnShoes {
    dependsOn "putOnSocks"
    doLast {
        println "Putting on Shoes."
    }
  }
  
  
Ici `putOnShoes`, pour s'executer, va dépendre de `putOnSocks`. Autrement dit `putOnSocks` sera s'executera en premier. Quand une tâche dépend de plusieurs autres taches, on passe a dependsOn une liste: `dependsOn = ["taskA", "taskB", "taskC"]`.
  
Dès lors qu'une tâche peut dépendre de plusieurs autres tâches, l'ordre d'execution de ces tâches peut devenir important. Gradle nous permet de gérer l'ordre grace aux commandes suivantes:  
  
  * **mustRunAfter** force le fait que la tache doit être executée après la tache. `taskB.mustRunAfter(taskA)` obligera d'executer la tache `taskB` apres `taskA`
  * **shouldRunAfter** est similaire mais moins strict que mustRunAfter du fait que la régle n'est pas respectée dans certains cas
  * **finalizedBy** n'est pas une régle d'ordre d'execution mais il nous permet par exemple(`taskX.finalizedBy taskY`) de toujours executer `taskY` peu importe le résultat de `taskX`.  
  
Voila pour la deuxième partie de la série portant sur gradle. Il y a énormement de choses a voir et nous ne pourrons pas tout couvrir ici. Mais avec ces deux articles nous avons de quoi debuter :). Le meilleur moyen d'aller en profondeur est de suivre le cours **[Gradle for Android and Java](https://www.udacity.com/course/viewer#!/c-ud867/)** sur **Udacity** et de tout le temps se reférer à la [documentation officielle](https://docs.gradle.org/current/userguide/userguide.html) pour comprendre les concepts de base. Petit bonus, la DSL du [plugin android](http://google.github.io/android-gradle-dsl/current/) :).
  