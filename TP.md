# TP Linux n°1

🔵🔵***⚠️❗️ METTRE UNE PHOTO POUR FAIRE LA PAGE DE GARDE ❗️⚠️***
![]()

## Sommaire

- ### [Introduction](https://github.com/LBROCHARD/TP_Linux/blob/main/TP.md#introduction-)
- ### [Mise en place des machines virtuelles](https://github.com/LBROCHARD/TP_Linux/blob/main/TP.md#mise-en-place-des-machines-virtuelles-)
- ### [Configuration des services réseaux](https://github.com/LBROCHARD/TP_Linux/blob/main/TP.md#configuration-des-services-r%C3%A9seaux-)
- ### [Installation de l'outil de gestion de ticket](https://github.com/LBROCHARD/TP_Linux/blob/main/TP.md#installation-de-loutil-de-gestion-de-ticket-)
- ### [Configuration de l'outil de gestion de ticket](https://github.com/LBROCHARD/TP_Linux/blob/main/TP.md#configuration-de-loutil-de-gestion-de-ticket-)
- ### [Ajout d'un plugin de remonté de poste client](https://github.com/LBROCHARD/TP_Linux/blob/main/TP.md#ajout-dun-plugin-de-remont%C3%A9-de-poste-client-)
- ### [Mise en place d'une sauvegarde du GLPI](https://github.com/LBROCHARD/TP_Linux/blob/main/TP.md#mise-en-place-dune-sauvegarde-du-glpi-)

## Introduction 👋

Le but de ce TP est la mise en place d'un parc informatique et d'un logiciel de ticketing pour permettre à la société **InforM** d'effectuer sa tâche de formation et d'entretien du materiel pour leurs clients.
Nous allons donc mettre un place un serveur GLPI qui vas permettre à la société de faire ces deux choses en même temps et sur un seul outil.
![](https://github.com/LBROCHARD/TP_Linux/blob/main/images/002%20infoM.png)

## Mise en place des machines virtuelles 💻

La Première étape est l'étape d'installation des VM (car il s'agit d'un TP), dans notre cas, on a besoin d'une machine sous Linux pour habriter le serveur GLPI, et d'une machine sous Windows pour émuler la machine d'un client. 
Pour la machine Linux, on vas utiliser un Debian 11, et pour la machine client, on vas simplement utiliser un windows 10. 
Leur mise en place est assez simple, on télécharge simplement des iso des different systèmes pour les installer sur VM ware, c'est une étape que l'on ne vas pas détailler.

Machine Debian :
![Machine Debian](https://github.com/LBROCHARD/TP_Linux/blob/main/images/004%20vm%20debian.png)
Machine Windows :
![Machine Windows](https://github.com/LBROCHARD/TP_Linux/blob/main/images/004%20vm%20windows.png)

## Configuration des services réseaux 📶

Par défaut sur VMware le réseau est en mode NAT, c'est à dire que le réseau de la machine virtuelle, agit comme si il s'agissait du réseau de l'ordinateur hôte.
Il conviens donc de passer le réseau en "Bridged" (réseau par pont) qui permet à la machine virtuelle d'agir comme si elle était une machine à part entière sur le même réseau que l'hôte, ce qui vas nous permettre de simuler un réseau avec un ordinateur serveur (la VM débian) et un ordinateur client (la VM windows).
Pour ce faire, il suffit d'aller dans les réglages de la machine virtuelle, et de modifier dans "Network Adaptater" le réseau de "NAT" à "Bridged" comme ci-dessous :
![](https://github.com/LBROCHARD/TP_Linux/blob/main/images/001%20passage%20de%20la%20debian%20en%20bridge.png)
⚠️ Un soucis rencontré fut la présence d'une sécurité sur le réseau d'Ynov, qui empechait au reseau de fonctionner sur une machine virtuelle configuré en bridged, pour le régler, nous nous sommes connecté au réseau d'un téléphone.

Sur le réseau du téléphone :

![sur le reseau du téléphone](https://github.com/LBROCHARD/TP_Linux/blob/main/images/003%20connection.png)

Sur le réseau d'Ynov :

![sur le reseau d'Ynov](https://github.com/LBROCHARD/TP_Linux/blob/main/images/003%20connection%20no.png) 

![sur le reseau d'Ynov](https://github.com/LBROCHARD/TP_Linux/blob/main/images/003%20connection%20echec.png) 

  🔵🔵***⚠️❗️ PARLER DU SSH ? ❗️⚠️***

## Installation de l'outil de gestion de ticket 🎫


Maintenant que les machines virtuels sonts configurées, il est temps d'installer l'outil de gestion de ticket.

### Installations :

Pour ce faire, il y a d'abord quelques installation préliminaires à prevoir :

⚠️ n'oubliez par pour de passer en mode root pour les mises a jour à l'aide de la commande : 
```su - ```
Cela permet d'obtenir toutes les permissions et donc de pouvoir installer des logiciels sans soucis.

Avant toutes choses, il faut faire la mise à jour des paquets :

```apt-get update && apt-get upgrade```

Puis l'installation succesive de Apache2, PHP et MariaDB, qui vont nous être utiles pour que le serveur fonctionne :

```apt-get install apache2 php libapache2-mod-php ```

```apt-get install php-imap php-ldap php-curl php-xmlrpc php-gd php-mysql php-cas```

```apt-get install mariadb-server```  
```mysql_secure_installation```

⚠️ Cette dernière installation vas vous posez beaucoups de questions de sécurités auquels il conviens de répondre oui (*Y majuscule*). Et elle vas aussi vous demander de renseigner un mot de passse, il s'agit du mot de passe de MariaDB, il ne faut pas l'oublier car il sera très utile.

Il faut ensuite installer des modules complémentaires :

```apt-get install apcupsd php-apcu ```

Et redémarrer les services d'Apache et de MariaDB en renseignant l'emplacement du fichier, suivie de la commande ``` restart```: 

```/etc/init.d/apache2 restart```
```/etc/init.d/mysql restart ```

### Création de la base de données :

L'étape suivante est d'entrer dans MariaDB afin de créer la base de donné.

On entre d'abord la commande ```mysql -u root -p ``` qui nous permet de controller linux via MariaDB en root (donc en utilisateur avec tout les droits) ⚠️nécésite le mot de passe de MariaDB !, puis on créé la base de donnée :

``` create database glpidb; ```

```grant all privileges on glpidb.* to glpiuser@localhost identified by "...";```

⚠️Ici on cède les privilèges à l'utilisateur, il faut donc remplacer les 3 petits points par un mot de passe

⚠️On utilise ```quit``` pour quitter MariaDB
 
 ### Installation du GLPI :
 
 Maintenant que le terrain est préparé, il est temps d'installer le GLPI, pour se faire, on se déplace vers le fichier src grâce à cette commande : 
 
 ```cd /usr/src/ ```
 
 Puis on vas chercher le glpi avec wget (qui permet de télécharger des fichiers depuis internet) :
 
 ```wget https://github.com/glpi-project/glpi/releases/download/9.3.3/glpi-9.3.3.tgz```
 
 On décompresse avec tar :
 
 ``` tar -xvzf glpi-9.3.3.tgz -C /var/www/html ```
 
 Et on cède les droits :
 
 ```chown -R www-data /var/www/html/glpi/```
 
 
## Configuration de l'outil de gestion de ticket 🔧

Maintenant que le GLPI est installé, nous allons pouvoir le configurer grâce à son interface web. L'un des grands avantages du GLPI est l'interface visuelle qui nous permet de le configurer à partir d'un simple navigateur internet !

### Setup du GLPI :

Pour ce faire il suffit d'ouvrir un navigateur sur un ordinateur present sur le reseau, et de taper l'adresse suivante : 

`http://.../glpi `

Il faut simplement taper l'adresse ip de la machine contenant le serveur GLPI suivit du `/glpi`.

⚠️ Pour obtenir cette adresse, on peut utiliser la commande `ip a ` et aller chercher l'adresse sous <BRODCAST,MULTICAST,UP,LOWER_UP>, juste après le "inet", comme ci dessous :

 ![](https://github.com/LBROCHARD/TP_Linux/blob/main/images/008%20ip%20a.png)
 
Et le résultat suivant devrait s'afficher sur votre navigateur :
 
![](https://github.com/LBROCHARD/TP_Linux/blob/main/images/008%20setup%20langue.png)
  
Il est donc temps de configurer le GLPI, tout d'abord, entrez votre langue d'utilisation.

![](https://github.com/LBROCHARD/TP_Linux/blob/main/images/008%20setup%20liscence%20.png)
 
 Puis acceptez les termes de la liscence.
 Et sur l'écran suivant :
 
 ![](https://github.com/LBROCHARD/TP_Linux/blob/main/images/008%20setup%20debut%20installation.png)
 
 Cliquez sur "installer".
 
 ![](https://github.com/LBROCHARD/TP_Linux/blob/main/images/008%20setup%20packets.png)
 
 S'en suit alors une liste de paquets avec la vérification de leur installation (tout devrait être vert)
 
 ⚠️ L'erreur tout en bas est une alerte de sécurité, car pour l'instant, notre serveur comporte des failles de sécurité, que nous pouvons ignorer dans le cadre d'un TP.

 ![](https://github.com/LBROCHARD/TP_Linux/blob/main/images/008%20etape%201.png)
 
 Cette fenêtre nous demande maintennat d'associer la base de donnée que nous avions créé sur MariaDB, avec le GLPI, il faut donc renseigner "localhost", dans *serveur SQL(MariaDB ou MySQL)*, "glpiuser" dans *utilisateur SQL*, et renseigner le mot de passe que vous avez choisis precedemment.
 
 ![](https://github.com/LBROCHARD/TP_Linux/blob/main/images/008%20test%20co%20base%20de%20donn%C3%A9es.png)
 
 Si tout c'est passé correctement, vous devriez maintenant voir votre base de donnée dans les options de selection de la base de données (nous l'avons appelées glpidb), séléctionnez là, puis appuyez sur continuer.
 
  ![](https://github.com/LBROCHARD/TP_Linux/blob/main/images/008%20initialisation%20bdd.png)
  
  Vous allez maintenant pourvoir initialiser la base de donnée en appuyant sur continuer.
 
 ⚠️ Attention ! laissez le temps à l'installation de ce faire une fois que vous appuyez sur continuer. Si vous le faites 2 fois, vous risquez de créer plusieurs bases de données, ce qui peut créer des soucis plus tard. 
Si vous avez malencontreusement cliquez plusieurs fois, allez voir à l'étape de [recommancer l'installation du glpi](https://github.com/LBROCHARD/TP_Linux/blob/main/TP.md#%EF%B8%8F-recomencer-linstalation-de-la-base-de-donn%C3%A9e-)

On vas maintenant vous demandez si vous voulez ou non transmettre des donées à des fins d'amelioration du service, et si vous voulez faire un don à l'entreprise qui le développe, libre à vous de choisir ce que vous voulez faire.

  ![](https://github.com/LBROCHARD/TP_Linux/blob/main/images/008%20initialisation%20termin%C3%A9.png)

L'installation est maintenant terminée, vous pouvez cliquez sur Utilisez GLPI et passer à l'étape suivante.

### (⚠️) Recomencer l'instalation de la base de donnée :

⚠️ Si vous avez rencontré un problème durant l'étape d'installation et de configuration de la base de données, il est possible de de la réinstaller, sinon, sautez cette étape.

Durant le TP, nous avons corrompus la base de données, mais nous avons réussis à la supprimer proprement pour la réinstaller.

Pour supprimer la base de donnée, rendez vous sur le terminal de votre machine Linux, (en root) et entrez dans MariaDB grâce à la commande `mysql`, puis executez la commande suivante : `DROP DATABASE glpidb;`, ce qui vas supprimer la base de donnée.  ⚠️ ici glpidb est le nom de ma database, il ne s'agit pas forcément toujours de celui-ci.
Si vous relancez votre page GLPI maintenant, vous allez avoir le message suivant :

![](https://github.com/LBROCHARD/TP_Linux/blob/main/images/008%20glpi%20erreur.png)

Maintenant, la base de donée à été supprimé, mais si vous recréez votre base de donnée maintenant, cela ne vas pas vous permettre de la reconfigurer, à cause du fichier *config_db.php* qui retient la configuration. Pour reconfigurer votre DB il vous faut donc supprimer ce fichier, que l'on peut trouver en faisant `cd /var/www/html/glpi/config` vous pouvez maintenant supprimer le fichier problématique avec `rm config_db.php`, et si vous retournez sur votre page GLPI :

![](https://github.com/LBROCHARD/TP_Linux/blob/main/images/008%20setup%20langue.png)

Boom ! Tout beau tout propre. Vous pouvez désormais recréer votre base de donné en faisant `mysql` pour entreer dans MariaDB puis refaites un base de donnée comme à [cette étape](https://github.com/LBROCHARD/TP_Linux/blob/main/TP.md#cr%C3%A9ation-de-la-base-de-donn%C3%A9es-) et reprendre à partir d'[ici]().

### Création utilisateur :

![](https://github.com/LBROCHARD/TP_Linux/blob/main/images/008%20connexion%20au%20glpi.png)

Vous pouvez maintenant vous connecter au glpi avec l'utilisateur par défaut,  
utilisez glpi comme identifiant et comme mot de passe.

⚠️Mais comme vous pouvez vous en douter, cet utilisateur n'est pas du tout sécurisé, il est donc important de se rendre dans *Administration*, puis *Utilisateurs*.

![](https://github.com/LBROCHARD/TP_Linux/blob/main/images/007%20utilisateurs%20.png)

Et il faut maintenant remplacer l'utilisateur *glpi* par un utilisateur avec un mot de passe sécurisé.


## Ajout d'un plugin de remonté de poste client 👨🏿‍💻

Nous allons maintenant installer FusionInventory, un plugin du GLPI qui vas nous permettre de remonter les informations d'un ordinateur client vers notre serveur GLPI. 

### Installations :

Tout d'abord, il faut installer le FusionInventory, pour ce faire, on met à jour notre système avec `apt-get update && apt-get upgrade` 

Et on télécharge le fusion inventory de la même manière que pour le GLPI :


`cd /usr/src `

`wget https://github.com/fusioninventory/fusioninventory-for-glpi/archive/glpi9.3+1.3.tar.gz `

`tar -zxvf glpi9.3+1.3.tar.gz -C /var/www/html/glpi/plugins  `


On lui attribue les droits :

`chown -R www-data /var/www/html/glpi/plugins`

Et on prépare la compatibilité du repertoire :


`cd /var/www/html/glpi/plugins `

`mv fusioninventory-for-glpi-glpi9.3-1.3/ fusioninventory/ ` 

### Installation sur l'interface web :

Pour finaliser l'installation sur l'interface web, connectez vous au GLPI avec l'utilisateur glpi, qui est le superutilisateur du GLPI.

***📸❗️ capture d'ecran GLPI configuration plugin ❗️📸***
![]()

Rendez vous ensuite dans *Configuration*, puis *Plugins*, ou vous devriez voir appraître le Fusion Inventory : 

![](https://github.com/LBROCHARD/TP_Linux/blob/main/images/009%20plugins%20fi.png)

Si c'est le cas, appuyer sur *Installer*, et lorsque le plugin est installé, appuyez sur *Activer* pour l'activer.

⚠️ Attention, cette étape d'installation peut prendre un moment, ne perdez pas patience, et faites attention à ne pas interrompre l'installation !

### Configuration du FusionInventory :

Allez maintenant dans *Administration*, puis *FusionInventory*

![](https://github.com/LBROCHARD/TP_Linux/blob/main/images/009%20ecran%20glpi%20menu%20configuration.png)

Le plugin est maintenant fonctionnel, mais un message nous informe que le CRON du GLPI ne fonctionne pas, en effet, pour que le GLPI fonctionne tout seul, on a besoin d'automatiser son fonctionnement en configurant le CRON pour envoyer une requette toutes les minutes au serveur.

Pour ce faire ouvrez le CRON :

`crontab -u www-data -e`

Selectionnez le choix 1, et ajouter la ligne suivante à la suite :

`*/1 * * * * /usr/bin/php5 /var/www/html/glpi/front/cron.php &>/dev/null`

Puis relancez le CRON :

`/etc/init.d/cron restart`

Rendez vous maintenant dans *Configuration*, puis *Actions Automatiques* et cherchez dans la liste le *TaskScheduler*

![](https://github.com/LBROCHARD/TP_Linux/blob/main/images/009%20task%20scheluder.png)

Cliquez dessus et et utilisez le bouton *Exécuter*

![](https://github.com/LBROCHARD/TP_Linux/blob/main/images/009%20executer%20action%20automatique.png)

Le message d'erreur devrait avoir disparus !


### Installation de l'agent FusionInventory : 

Il faut maintenant installer l'agent du FusionInventory sur le poste client, basculez donc sur votre VM Windows, et rendez vous sur ce site :     http://fusioninventory.org/documentation/agent/installation/

Pour pouvoir télécharger l'agent dans sa version Windows.

![](https://github.com/LBROCHARD/TP_Linux/blob/main/images/010%20ecran%20site%20agent.png)

Une fois téléchargé, executez l'installeur en mode administrateur, et complétez l'installation, jusqu'à arriver aux choix des composants :

![](https://github.com/LBROCHARD/TP_Linux/blob/main/images/010%20fusion%20agent%20.png)

Selectionnez *Complète* dans *Type d'installation* pour avoir tout les composants (comme ci-dessus).

Selectionnez ensuite l'emplacment de l'installation et cliquez sur suivant.

L'écran suivant est unécran nous permettant de choisir à quel serveur l'agente enverra les resultats obtenues sur le poste, il faut donc renseigner l'emplacement de son réseau comme ceci :

![](https://github.com/LBROCHARD/TP_Linux/blob/main/images/010%20fusion%20agent%202.png)

Il suffit pour cela de copier la ligne suivante : `http://<serveur>/glpi/plugins/fusioninventory/` et de remplacer  <*serveur*> par l'adresse ip de votre machine serveur (Debian pour nous).

⚠️ N'oubliez pas de cocher la case *Installation rapide (n’affiche pas les autres options Windows)*

Appuyez sur *Installer* et une fois que c'est fait, appuyez sur *Suivant* pour finaliser l'installation.

### Vérification de l'installation : 

Nous allons maintenant vérifier qu'un lien c'est bien créé entre l'agent et le GLPI. Sur votre machine client, ouvrez un navigateur internet et rentrez l'adresse : **http://localhost:62354**, cela vas vous ouvrir une page qui doit ressembler à cela : 

![](https://github.com/LBROCHARD/TP_Linux/blob/main/images/010%20agent%20page%20web.png)

Verifiez que tout est correct et cliquez sur *Force an Inventory*.

Retournez maintenant sur votre interface GLPI, et rendez vous dans *Administration*, puis *Fusion Inventory*, et dans le sous-menu *Général*, puis *Gestion des Agents*

Vous devriez alors voir appraitre votre machine cliente dans la liste :

![](https://github.com/LBROCHARD/TP_Linux/blob/main/images/010%20machine%20cliente%20dans%20la%20liste.png)



## Mise en place d'une sauvegarde du GLPI 💾













