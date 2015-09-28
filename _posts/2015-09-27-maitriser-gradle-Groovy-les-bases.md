---
layout: post
title: Maitriser gradle. Groovy, les bases
---


[Gradle](https://gradle.org/) est un outil de build créer par la sociéte Gradle Inc. L'idée est d'avoir une manière simple et puissante de décrire la construction de nos apps.  
Pourquoi en parler. La raison est tres simple. Depuis quelques années maintenant Google a fait le choix de Gradle pour construire les apps android. Si vous utilisez android studio, vous faites du gradle peut-être sans le savoir vu qu'il est possible de faire le minimum sans éditer le fichier de `build.gradle` directement comme ajouter une dépendence, ajouter un builType, etc. 
Gradle a étè conçu avec le language Groovy qui est un language de la JVM. Très puissant et super sympa à utiliser quand on vient du monde verbeux de Java. 
Le but de cette série d'articles sera à la fin de pouvoir manipuler notre build en fonction des besoins de notre app. Comme, par exemple, copier les différents fichier apks générés dans un serveur distant. Mais avant cela il faut d'abord un prérequis. Groovy!

##Groovy, trop cool!

Groovy est un language de programmation très séduisant et on sent le souci de vouloir créer un language simple et puissant pour les développeurs Java tout en les gardant dans cette écosystème Java/JVM qui est la force de Java. Ce qui permet de rentrer dans le monde groovy en douceur. La **class generator** est l'outil qui va prendre le code source Groovy, le parser et générer du bytecode qui est compatible avec le bytcode Java. D'ou la possibilité de pouvoir utiliser du code Java dans du Groovy et du groovy dans du Java. Un excellent point pour les secptiques.  
Ci-dessous quelques éléments sympas de groovy: 

	boolean isFirsGroovy = true;
	//some stuff...
	if(isFirsGroovy) {
		System.out.println("Hello World!");
	} else {
		System.out.println("Welcome back!");
	}
	
Ceci est un code qui ressemble grandement à ce que l'on connait sur Java. Mais c'est aussi du code groovy :). Rendons le plus simple:

	def isFirstGroovy = true
	//some stuff...
	if(isFirsGroovy) {
		println "Hello World!"
	} else {
		println "Welcome back!"
	}

Comme on peut le remarquer, les point-virgules pour délimiter une instruction ne sont pas obligatoires. Sauf si on souhaite avoir plusieurs instructions sur une même ligne.  
Les parentheses ne sont pas obligatoire aussi pour une methode ayant au moins un paramétre. Ce qui élimine le code inutile et met en évidence l'essentiel. 
Groovy importe par défaut certains packages pour nous. il s'agit de:  

* java.io.*
* java.lang.*
* java.math.BigDecimal
* java.math.BigInteger
* java.net.*
* java.util.*
* groovy.lang.*
* groovy.util.*


Encore plus cool ce sont les **propreties**. Dans grovvy quand une classe expose par exemple des methodes `getNom()` et `setNom()`, cela veut dire que `nom` est une property de la classe. Ce qui est sympa c'est qu'on a pas besoin de creer les accesseurs nous même. Groovy s'occupe de cela:

	class Book{
		String title
		String author
	}
	
	def book = new Bookk()
	book.setTitle('Groovy in action')
	assert book.getTitle() == 'Groovy in action'
	book.title = 'Gradle in action'
	assert book.title == Gradle in action	
Comme on le voit tout devient simple, le code lisible. `book.title = ''` fait appel au setter et non à une instanciation directe. pour le vérifier on va créer notre setter:

	class Book{
		String title
		String author
		
		def setTitle(def aTitle){
			println 'Setting the title'
			title = aTitle
		}
	}
	def book = new Bookk()
	book.title = 'Groovy in action'
	//output: 'Setting the title'
	
On utilise bien le setter :). Ce type de code est beaucoup utilisé dans du code groovy pour encore une fois plus de lisibilité.

Groovy étant un `language dynamique`, on n'a pas besoin de spécifier le type d'une variable. On met à la place le mot cle **def**. Et encore mieux une meme variable peux changer de type! 

	def book = new Book()
	book.title = 'Groovy in action'
	book = true
	book = 1
	println book // output => 1
Ce code marchera sans souci! c'est magique non.  
Maintenant quand la variable est typée, on ne peut pas changer de type. On retrouve ici le typage fort de Java qui peut être utile dans certain cas. 

	Book book = new Book()
	book.title = 'Groovy in action'
	book = true
	
Ce code ne marchera pas. On aura une `GroovyCastException`.

Les listes et les maps. Ici aussi groovy simplifie beaucoup les choses et offres énormement de possibilités pour manipuler les listes et les maps

	//creer une liste
	def list = [1,2,3,4]
	//changer la valeur
	list[0] = 9
	//ajouter la valeur 20 a la liste
	list.add 20
	//Pas de ArrayOutOfbound :)
	println list[6] // retourne null
	//Donc rajouter une valeur a une position inexistente est possible
	list[8] = 12
Le plus cool ici c'est la possiblite de rajouter un élèment à une position qui est en dehors de la taille de la liste. Et on n'a pas besoin de spécifier le type de données contenu dans la liste comme on le ferait avec java (`ArrayList<String>`). Pour créer une liste vide:`def list = []`

	// Une map vide
	def map = [:]
	def map1 = [nom: 'Sidibe', prenom: 'Souleymane', surnom: 'soulesidibe']
	assert map1.nom == 'Sidibe'
	//ajouter/modifier un nouveau couple cle valeur a la map
	map1['city'] = 'Dakar'
	
Quand la clé existe déjà, c'est une moficiation sinon on ajoute le couple.
Les maps sont beaucoup utilisés dans gradle. 

Les chaines de caracteres. Encore beaucoup de bonnes choses ici. 
Déjà pour définir une chaine de caracteres, il y a deux possibilites. Les quotes simples `''` et les doubles quotes `""`. Il y a une grande difference entre les deux. La plus notable et la possiblité d'avoir des placeholders avec les `""`:

	def nom = 'souleymane sidibe'	
	println "Mon nom est $nom"
	
Encore mieux, on peut éxecuter du code simple à l'aide d'accolades `${}`:
	
	def nom = 'souleymane sidibe'	
	println "Mon nom inverse est ${nom.reverse()}"
	
Pas mal non? J'adore groovy!

Groovy supporte également les **closures**. La définition officielle:
  
> A closure in Groovy is an open, anonymous, block of code that can take arguments, return a value and be assigned to a variable. A closure may reference variables declared in its surrounding scope
	
Un exemple de closure dans le GDK:

    def list = [1,2,3,4,5,6,7]
    list.each({value -> 
        println "Valeur: $value"
    })
Ceci est une façon simple de boucler une liste.  
Le bout de code `{value -> println "Valeur: $value"}` est un closure et est executé sur chaque élément de la liste grace à la méthode `each` qui prend en paramétre un closure.  
Les closures ont la frome `{ [closureParameters -> ] statements }`. les parametres sont optionnels. Quand on a qu'un seul parametre on peut ne pas le specifier et utiliser `it`. Le closure plus haut peut être remplaceé par  `{println "Valeur: $it"}`.  
Comparé aux méthodes en groovy, les closures ont accés aux variables du scope parent: 

	def myMethod() {
		def value = 3    
    	def doubleValue = { value * 2 }    
    	println doubleValue()
	}
	myMethod()
	// output => 6
	
On peut aussi les passer en paramétre à une methode:

	//methode qui execute un closure
    def myMethod(Closure closure){
        closure()
    }
    //passage en param d'un closure
    myMethod({ println "This is a closure"})
    
    //Les parentheses ne sont pas obligatoires
    myMethode { println "This is a closure" }
    
    //Resemble beaucoup a ce qu'on voit dans le fichier build de 	 //gradle
    android {
    	applicationId "com.soulesidibe"
    }
    
Quand on commence a les utiliser on ne peut plus s'en passer! Je n'ai pas tout dit mais il faut bien s'arrêter...

## Conclusion

Voila voila une petite intro à groovy. Ceci n'est pas un cours sur groovy. Pour mieux comprendre les concepts derrière, quelques liens pour commencer: 

* [Un excellent tuto pour commencer](http://learnxinyminutes.com/docs/groovy/)
* [Un chapitre du livre `Groovy in action`](https://dl.dropboxusercontent.com/u/48451250/GroovyiA2_CH02.pdf)
* [La documentation officielle](http://www.groovy-lang.org/documentation.html)

Il y a de quoi se documenter et connaitre le minimum requis pour bien comprendre les concepts de gradle.
Le prochain article portera sur les premiers pas avec gradle! See you :).