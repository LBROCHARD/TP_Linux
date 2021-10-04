# TP2
📷 ⚠️ ❗️❗️ 
📷❗️❗️  image de linux tp2  ❗️❗️📷


## Sommaire



### [Introduction](https://github.com/LBROCHARD/TP_Linux/blob/main/TP2.md#introduction-1)

### [Installation des Machines Virtuels](https://github.com/LBROCHARD/TP_Linux/blob/main/TP2.md#installation-des-machines-virtuels-1)

### [Configuration d'un site web avec apache](https://github.com/LBROCHARD/TP_Linux/blob/main/TP2.md#configuration-dun-site-web-avec-apache-1)

### [Installation de PHP sur le serveur appache](https://github.com/LBROCHARD/TP_Linux/blob/main/TP2.md#installation-de-php-sur-le-serveur-appache-1)

### [.]()





## Introduction

![](https://github.com/LBROCHARD/TP_Linux/blob/main/images/002%20infoM.png)

Suite à un incendie dans la société qui hébergait le site web de la société inforM, cette dernière à décidée d'héberger elle même son site web.
Pour ce faire, nous allons mettre en place un hébergement de serveur web avec Apache.


## Installation des Machines Virtuels

Dans le cadre du TP, nous allons utiliser une machie virtuelle sur VMWare. 
Nous allons utiliser une Debian 11 car il s'agit de la dernière version d'un linux très stable, et parce que nous avons besoin d'un linux pour installer le serveur Apache.
L'installation d'une machine virtuelle consiste simpement à télécharger et installer un ISO du système souhaité sur VMWare. Cette étape n'étant pas l'interet de TP elle ne sera pas développée.

La Machine virtuelle Debian 11 :

<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images/004%20vm%20debian.png" width="500"/>

## Configuration d'un site web avec apache

Pour mettre en place notre serveur web, nous allons utiliser Apache HTTP serveur (que nous allons surnomer Apache). 
Apache est un logiciel libre développé par la fondation du même nom, est un serveur HTTP, ce qui veut dire, un serveur informatique capable de répondre à des requettes Web.
Apache étant libre et l'un des serveur HTTP les plus utilisé du marché, il est parfaitement indiqué de l'utilisé pour ce TP.

La première étape est donc d'installer Apache sur notre Debian avec la commande suivante :

```sudo apt-get install apache2```

Apache est capable de gérer plusieurs sites web en même temps, on les appelles Virtuels Hosts. C'est la requette HTTP qui renseignera le site à consulter.
Pour Ajouter un nouveau Virtual Host, il faut créer un nouveau fichier `.conf` dans le repertoire `/etc/apache2/sites-available/` avec la commande `cd /etc/apache2/sites-available/`.

<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images2/2%20contenue%20cd%20etc-apache-sites%20available%20.png"/>

On peut y voir les fichiers par default. Nous allons donc ajouter un nouveau Virtuel Host dans ce repertoir grâce à la commande `touch`.
Nous allons appeler notre fichier `ww.test.com.conf` :

⚠️ Attention, pour ajouter ou modifier un fichier dans ce repertoir il nous faut les autorisation Admin, donc on rajoute un `sudo`devant la commande.

⚠️ Si vous ne voulez pas vous embeter à utiliser sudo devant chaque commande, vous pouvez passer en mode root avec la commande `su -`. Cette commande vous demandera votre mot de passe, puis vous entrerez en mode root. Si vous êtes en mode root, vous pouvez ignorer tout les sudo avant les commandes.

<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images2/3%20sudo%20touch.png"/>

On vas maintenant ouvrir ce fichier avec la commande `sudo nano www.test.com.conf` et y entrer le code suivant :

<pre>
<code>
  &lt;VirtualHost *:80&gt;
    ServerName www.test.com
    ServerAlias test.com
    ServerAdmin webmaster@test.com
    DocumentRoot /var/www/html/www.test.com
  &lt;/VirtualHost *:80&gt;
</code>
</pre>

Le \*:80 correspond ici au port sur lequel le serveur va écouter. 
Le *ServerName* correspond au nom utilisé par le Virtuel Host sur notre server, il ne peut y avoir qu'un seul *ServerName* mais il peut y a voir plusieurs *ServerAlias* (qui correspondent a des noms alternatifs au cas ou le *ServerName* ne fonctionne pas).
Le *ServerAdmin* correspond à l'adresse mail qui sera utilisé sur certaines erreurs pour entreer en contact avec le moderateur.
Et enfin le *DocumentRoot* est l'emplacement des fichiers de votre site web.

⚠️ Il existes encore de nombreux paramètres que nous aurions pu régler, mais dans le cadre d'un TP, cette configuration suffira.

Maintenant que notre VirtualHost a été créé, il nous reste donc a implémenter notre site web.
Pour ce faire, il faut se rendre à l'emplacement des fichiers de notre site avec la commande `cd /var/www/html/`
et y créer un fichier `index.html` avec la commande `sudo touch index.html` vous pouvez à present y déposer votre site (pour le tp, nous nous contenterons d'un simple bonsoir).

📷❗️❗️  index.html bonsoir ❗️❗️📷


Puis pour lier 

❗️❗️parler de ce qu'est un nom de domaine (DNS et tt ça)

❗️❗️comment on aurait put le sécuriser plus


## Installation de PHP sur le serveur appache

les deux manière de faire 
la bonne manière de faire 


