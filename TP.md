# TP Linux n°1

***⚠️❗️ METTRE UNE PHOTO POUR FAIRE LA PAGE DE GARDE ❗️⚠️***

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

  ***⚠️❗️ PARLER DU SSH ? ❗️⚠️***

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


 ***⚠️❗️ EST CE QU'IL FAUT UTILISER CA ? : ```apt-get install phpmyadmin``` ❗️⚠️***
 
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

 ***📸❗️ INSERER CAPTURE DECRAN DE IP A ❗️📸***
 
Et le résultat suivant devrait s'afficher sur votre navigateur :
 
***📸❗️ capture d'ecran glpi setup langue  ❗️📸***
  
Il est donc temps de configurer le GLPI, tout d'abord, entrez votre langue d'utilisation.

***📸❗️ capture d'ecran glpi setup licence  ❗️📸***
 
 Puis acceptez les termes de la liscence.
 Et sur l'écran suivant :
 
 ***📸❗️ capture d'ecran glpi setup debut installation  ❗️📸***
 
 Cliquez sur "installer".
 
 ***📸❗️ capture d'ecran glpi setup paquets  ❗️📸***
 
 S'en suit alors une liste de paquets avec la vérification de leur installation (tout devrait être vert)
 
 ⚠️ L'erreur tout en bas est une alerte de sécurité, car pour l'instant, notre serveur comporte des failles de sécurité, que nous pouvons ignorer dans le cadre d'un TP.
 
 ***📸❗️ capture d'ecran glpi setup etape 1 config conexion  ❗️📸***
 
 Cette fenêtre nous demande maintennat d'associer la base de donnée que nous avions créé sur MariaDB, avec le GLPI, il faut donc renseigner "localhost", dans "serveur SQL(MariaDB ou MySQL), "glpiuser" dans "utilisateur SQL", et renseigner le mot de passe que vous avez choisis precedemment.
 
 ***📸❗️ capture d'ecran glpi setup etape 2 test co base donné  ❗️📸***
 
 Si tout c'est passé correctement, vous devriez maintenant voir votre base de donnée dans les options de selection de la base de données (nous l'avons appelées glpidb), séléctionnez là, puis appuyez sur continuer.
 
  ***📸❗️ capture d'ecran glpi setup etape 3 initialisation bdd  ❗️📸***
  
  Vous allez maintenant pourvoir initialiser la base de donnée en appuyant sur continuer.
 
 ⚠️ Attention ! laissez le temps à l'installation de ce faire une fois que vous appuyez sur continuer. Si vous le faites 2 fois, vous risquez de créer plusieurs bases de données, ce qui peut créer des soucis plus tard. 
Si vous avez malencontreusement cliquez plusieurs fois, allez voir à l'étape de ![recommancer l'installation du glpi]()

On vas maintenant vous demandez si vous voulez ou non transmettre des donées à des fins d'amelioration du service, et si vous voulez faire un don à l'entreprise qui le développe, libre à vous de choisir ce que vous voulez faire.

  ***📸❗️ capture d'ecran glpi setup etape 6 initialisation terminée  ❗️📸***

L'installation est maintenant terminée, vous pouvez cliquez sur Utilisez GLPI et passer à l'étape suivante.

### ⚠️ Recomencer l'instalation de la base de donnée :

⚠️ Si vous avez rencontré un problème durant l'étape d'installation et de configuration de la base de données, il est possible de de la réinstaller, sinon, sautez cette étape.

Durant le TP, nous avons corrompus la base de données, mais nous avons réussis à la supprimer proprement pour la réinstaller.

### Accès au GLPI :


## Ajout d'un plugin de remonté de poste client 👨🏿‍💻











## Mise en place d'une sauvegarde du GLPI 💾













