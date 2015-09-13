---
layout: post
title: Android 6.0 Marshmallow est la! (Part 2)
---


L'un des changements les plus importants de la release 6.0 est le nouveau mode de
fonctionnement des permissions. A partir de Android Marshmallow, lors de l'installation d'une nouvelle application sur le playstore, le user n'aura plus à autoriser une liste enorme de permissions. Les demandes d'autorisations se feront au runtime et au besoin. Exemple, l'application a besoin d'écrire sur la mémoire externe, il lui suffira de faire la demande à l'utilisateur via une AlertDialog gérée par le systeme et il pourra accepter ou pas la demande du développeur. Côte client, c'est très intéressant vu qu'on a la main sur qui a droit de faire quoi.  
Côte développeur, en fonction des categories des permissions qu'on a defini dans le Manifest, on aura plus ou moins du boulot pour rendre compatible nos applications. On verra ensemble les etapes permettant de rendre compatible nos apps sans trop de difficultés.

##Les permissions

Je ne vais pas revenir sur l'importance des permissions. Parcontre, il est important de connaitre les différents groupes de permissions. La raison est simple. Avec Android 6.0 quand on fait la demande d'une permission, le syteme nous donne acces au groupe ou pas. Ce qui permet au user de ne pas autoriser un par un une liste de permissions appartenant au même groupe. 
Prenons le cas d'une application souhaitant gérer nos contacts. Pour récuperer la liste des contacts, elle a besoin de la permission de lecture des contacts donc elle demande l'accès a la permission `READ_CONTACTS`. A un certain moment, elle veut modifier un contact, elle a besoin pour cela de la permission d'écriture. Elle demande le droit d'accès à la permission `WRITE_CONTACTS` et comme elle avait déjà l'accès au groupe CONTACTS, elle a automatiquement le droit de modifier. Le user n'a pas à autoriser une seconde fois.
Ceci simplifie grandement les choses pour le user mais aussi pour le developpeur. 

###[Les groupes de permissions](http://developer.android.com/reference/android/Manifest.permission_group.html)
![permissions groups](/assets/permission_groups.png)

##Comment ca marche

###En théorie

Tout d'abord, avec la multitude de versions d'android disponible sur le marché, nous développeurs devons penser à la retro compatibilité de nos apps(oui rien de bien nouveau...). 
La déclaration des permissions utilisées dans le manifest ne change pas rien que pour éviter d'avoir des `SecurityException` pour les versions avant la 6.0.

A l'installation d'une application une liste limitée de permissionq est granted à l'application. Le développeur n'aura pas à faire la demande pour avoir l'accès.
Il s'agit des permissions dont le niveau protection est `PROTECTION_NORMAL` comme par exemple la permission internet. Evidemment, on a aussi les permissions `PROTECTION_DANGEROUS` qui sont accessibles qu'après autorisation du user au runtime. Comme récupèrer les contacts. Pour plus d'infos sur les niveaux de protection voir [la doc offcicielle](http://developer.android.com/guide/topics/manifest/permission-element.html). La liste des permissions et leur niveau de protection y est [dispo!](http://developer.android.com/reference/android/Manifest.permission.html)

Quand l'app a besoin d'effectuer une action qui requiert une autorisation, il fait la demande au systéme qui affiche ou pas une Dialog(en fonction qu'il est deja l'autorisation ou pas) à l'utilisateur qui peut autoriser ou pas la demande d'accès. Le systéme renvoie un callback à l'app pour lui notifier de la réponse du user. Ce nouveau modèle change le mode de fonctionnement de nos apps et voici quelques points à prendre en compte: 

* Always check for permissions: Quand l'app a besoin d'une permission pour procéder à un traitement, il faut toujours verifier si on a la permission. Nous verrons comment le faire. Encore une fois les permissions de niveau `PROTECTION_NORMAL` n'ont pas besoin d'être vérifiées. 

* Handle Lack of Permissions Gracefully: Il est possible que l'utilisateur n'autorise pas l'accès à certaines fonctionnalités. Il faut garder à l'esprit ce fait. Ce qui est conseillé dans ce cas la est de désactiver la fonctionnalité qui depend de cette permissions et expliquer au user le pourquoi de la non disponibilite de la feature. 

* Permissions are Revocable: Le user peut à tout moment révoquer une  permission à n'importe quelle app qu'il a installée. Et l'app n'est pas notifiée de cette révocation. D'ou l'importance de tout le temps vérfifier les droits d'accès.

###En pratique
Voici un petit projet pour illustrer un peu le concept. C'est une app qui affiche dans le logcat le nom du user du device. Simple. 
Pour lancer le projet, il faut deja mettre à jour le SDK pour récuperer android 6.0. Puis creer un AVD tournant sous Marshmallow. 
Le projet est disponible sur [GitHub](https://github.com/soulesidibe/AndroidPermission)

Pour afficher le profile du user, on utilise le ContentProvider qui expose tout ce qui est en rapporte avec les contacts: 

{% highlight java %}
 	// Sets the columns to retrieve for the user profile
	String[] mProjection = new String[]{
		ContactsContract.Profile.DISPLAY_NAME_PRIMARY,
	};

	// Retrieves the profile from the Contacts Provider
	mProfileCursor = getContentResolver().query(
	        ContactsContract.Profile.CONTENT_URI,
	        mProjection,
	        null,
	        null,
	        null);

	if (mProfileCursor == null) {
	    return;
	}
	mProfileCursor.moveToFirst();
	while (!mProfileCursor.isAfterLast()) {
	    Log.d("Permission Test", "Display Name >> " + mProfileCursor.getString(0));
	    mProfileCursor.moveToNext();
	}
{% endhighlight %}

Pas la peine de revenir sur cette partie. Ce n'est pas le but. Ce qui est important ici c'est qu'il faut avoir la permission `android.permission.READ_CONTACTS` pour pouvoir lire le profil du user. Donc pour ne pas avoir de SecurityException, il suffit de rajouter `<uses-permission android:name="android.permission.READ_CONTACTS" />` au niveau du manifest. 
La tous les devices sous android API 22 et above marcheront. Maintenant ce n'est pas le cas pour l'API 23 vu que la permission `READ_CONTACTS` est considerée comme `PROTECTION_DANGEROUS`. L'app doit demander la permission au runtime avant de pouvoir proceder au traitement. Ca nous fait du code de ce type à avoir un peu partout dans notre code :

{% highlight java %}
if (Build.VERSION.SDK_INT <= Build.VERSION_CODES.LOLLIPOP_MR1) {
    //permission granted at install time        
}else{
	//Check if permission granted
}
{% endhighlight %}

Ce qui n'est pas top pour la codebase de notre projet. Mais avec la support library (`23.0.0`) on n'a pas a vérifier cela. On pourra directement vérifier si on a le droit de lire les contacts et la support library s'occupera de tester si on est sous android 6 ou pas. 
Pour verifier une permission, le code sera le même à chaque fois que l'on desire checker une permission: 

{% highlight java %}
if(checkSelfPermission(Manifest.permission.READ_CONTACTS)
    != PackageManager.PERMISSION_GRANTED) {
    if (shouldShowRequestPermissionRationale
    	(Manifest.permission.READ_CONTACTS)) {
    	//Explain why read contact permission is required
    }

    requestPermissions(
    	new String[]{Manifest.permission.READ_CONTACTS},
		PERMISSION_REQUEST_CONTACTS
	);
} else {
	//Permission read contact granted
    getProfile();
}
{% endhighlight %}

Detaillons un peu ce code: 
`checkSelfPermission(Manifest.permission.READ_CONTACTS)` permet de vérifier si on a la permission `READ_CONTACTS`. Si c'est le cas la méthode retourne un `PERMISSION_GRANTED`. On peut proceder à la lecture des informations du contact sinon un `PERMISSION_DENIED` nous oblige à faire une demande au syteme via le methode `requestPermissions(String[], int)`. 
Le syteme affiche donc une AlertDialog au user lui demandant d'autoriser ou pas la demande:
![Permission request](/assets/permission_request.png)

Le resulat de la demande sera traité dans le `onRequestPermissionsResult` : 

{% highlight java %}
if (requestCode == PERMISSION_REQUEST_CONTACTS) {
    if (grantResults[0] == PackageManager.PERMISSION_GRANTED) {
        // Permission has been granted. 
        getProfile();
    } else {
        // Permission request was denied.
    }
}
{% endhighlight %}

La constante `PERMISSION_REQUEST_CONTACTS` est fournie dans la methode `requestPermissions` et permet d'identifer la demande.  
Il peut arriver que le user deny notre demande. En plus de désactiver la fonctionnalité dont dépend cette permission, il faut pouvoir expliquer au user le pourquoi de cette permission. La méthode `shouldShowRequestPermissionRationale()` nous indique le bon moment pour faire cela.  
Les methodes `requestPermissions()`, `onRequestPermissionsResult()`, `shouldShowRequestPermissionRationale()` ont été ajoutées à android 6.0 donc ne sont pas dispo pour 5.x et les autres. Mais encore ici, la support library nous gére cela. Il suffit d'utiliser `AppCompatActivity` ou `Fragment` de la support-v4 pour y avoir accès.

##Mettre à jour une application existente

Le cas traité plus haut concerne plus une nouvelle app. Dans le cas d'une application qui target android 5.x, cela peut être un peu moins simple à gérer. Mais en suivant les étapes suivantes, on peut le s'en sortir relativement bien.  

Tout d'abord il faut identifier au niveau du manifest toutes les permissions déclarées. C'est aussi locasion de voir la pertinence de ces permissions pour l'app. Il est courant de déclarer une tonne de permissions dont on n'a pas le besoin. 
Ci-dessous la liste des permissions de niveau normal et dangereux. Cela vous permettra d'ientifier assez vite quelles permissions devront être revues côté code pour prendre en compte android 6.0. Après, il suffira de dérouler ce qui a été expliqué plus haut et tout devrait bien se passer :).

####Liste des permissions de niveau `PROTECTION_NORMAL`

Ces permissions peuvent être utilisées sans procéder à la demande au runtime. Elle seront granted automatiquement à l'installation de l'app:

* `android.permission.ACCESS_LOCATION_EXTRA_COMMANDS`
* `android.permission.ACCESS_NETWORK_STATE`
* `android.permission.ACCESS_NOTIFICATION_POLICY`
* `android.permission.ACCESS_WIFI_STATE`
* `android.permission.ACCESS_WIMAX_STATE`
* `android.permission.BLUETOOTH`
* `android.permission.BLUETOOTH_ADMIN`
* `android.permission.BROADCAST_STICKY`
* `android.permission.CHANGE_NETWORK_STATE`
* `android.permission.CHANGE_WIFI_MULTICAST_STATE`
* `android.permission.CHANGE_WIFI_STATE`
* `android.permission.CHANGE_WIMAX_STATE`
* `android.permission.DISABLE_KEYGUARD`
* `android.permission.EXPAND_STATUS_BAR`
* `android.permission.FLASHLIGHT`
* `android.permission.GET_PACKAGE_SIZE`
* `android.permission.INTERNET`
* `android.permission.KILL_BACKGROUND_PROCESSES`
* `android.permission.MODIFY_AUDIO_SETTINGS`
* `android.permission.NFC`
* `android.permission.READ_SYNC_SETTINGS`
* `android.permission.READ_SYNC_STATS`
* `android.permission.RECEIVE_BOOT_COMPLETED`
* `android.permission.REORDER_TASKS`
* `android.permission.REQUEST_INSTALL_PACKAGES`
* `android.permission.SET_TIME_ZONE`
* `android.permission.SET_WALLPAPER`
* `android.permission.SET_WALLPAPER_HINTS`
* `android.permission.SUBSCRIBED_FEEDS_READ`
* `android.permission.TRANSMIT_IR`
* `android.permission.USE_FINGERPRINT`
* `android.permission.VIBRATE`
* `android.permission.WAKE_LOCK`
* `android.permission.WRITE_SYNC_SETTINGS`
* `com.android.alarm.permission.SET_ALARM`
* `com.android.launcher.permission.INSTALL_SHORTCUT`
* `com.android.launcher.permission.UNINSTALL_SHORTCUT`

####Liste des permissions de niveau `PROTECTION_DANGEROUS`

La liste des permissions que vous devez obligatoirement vérifier au runtime quand votre app tourne sous android 6.0 et plus 

![PROTECTION_DANGEROUS](/assets/dangerous.png)

###Bonus
Quelques liens et videos utils pour aller en profondeur et surtout mieux comprendre le concept: 

* [Permisions](http://developer.android.com/preview/features/runtime-permissions.html) [article]
* [Why runtime permissions shouldn't scare you](https://plus.google.com/+AndroidDevelopers/posts/FqgHUevqHiK) [article]
* [Building better apps with Runtime Permissions](http://android-developers.blogspot.sn/2015/08/building-better-apps-with-runtime.html?linkId=16779377) [article]
* [Android RuntimePermissionsBasic Sample](https://github.com/googlesamples/android-RuntimePermissionsBasic) [github]
* [My Sample code](https://github.com/soulesidibe/AndroidPermission) [github]
* [Android, May I. Droidcon NYC](https://www.youtube.com/watch?v=WGz-alwVh8A) [video]
* [Google I/O 2015 - Android M Permissions](https://www.youtube.com/watch?v=f17qe9vZ8RM) [video]

C'est tout pour les permissions. C'est un sujet assez important à ne pas négliger si on souhaite que notre app supporte android 6.0. Il faut prendre le temps de comprendre les choses et de les tester.
Je suis ouvert à toutes questions, suggestions en commentaire ou sur twitter(@soulesidibe).
