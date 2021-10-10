# TP2

📷 ⚠️ ❗️❗️ 

📷❗️❗️  image de linux tp2  ❗️❗️📷


## Sommaire



### [Introduction](https://github.com/LBROCHARD/TP_Linux/blob/main/TP2.md#introduction-1)

### [Installation des Machines Virtuels](https://github.com/LBROCHARD/TP_Linux/blob/main/TP2.md#installation-des-machines-virtuels-1)

### [Configuration d'un site web avec apache](https://github.com/LBROCHARD/TP_Linux/blob/main/TP2.md#configuration-dun-site-web-avec-apache-1)

### [Mise en place d'un DNS](https://github.com/LBROCHARD/TP_Linux/blob/main/TP2.md#mise-en-place-dun-dns-1)

### [Mise en place d'un certificat SSL](https://github.com/LBROCHARD/TP_Linux/blob/main/TP2.md#mise-en-place-dun-certificat-ssl-1)

### [Configuration d'une solution de haute disponibilité](https://github.com/LBROCHARD/TP_Linux/blob/main/TP2.md#configuration-dune-solution-de-haute-disponibilit%C3%A9-1)

### [Conclusion](https://github.com/LBROCHARD/TP_Linux/blob/main/TP2.md#conclusion-1)



## Introduction

![](https://github.com/LBROCHARD/TP_Linux/blob/main/images/002%20infoM.png)

Suite à un incendie dans la société qui hébergait le site web de la société inforM, cette dernière à décidée d'héberger elle même son site web.
Pour ce faire, nous allons mettre en place un hébergement de serveur web avec Apache, puis configurer un DNS, et mettre en place un certificat SSl.



## Installation des Machines Virtuels

Dans le cadre du TP, nous allons utiliser une machie virtuelle sur VMWare. 
Nous allons utiliser une Debian 11 car il s'agit de la dernière version d'un linux très stable, et parce que nous avons besoin d'un linux pour installer le serveur Apache.
L'installation d'une machine virtuelle consiste simpement à télécharger et installer un ISO du système souhaité sur VMWare. Cette étape n'étant pas l'interet de TP elle ne sera pas développée.

La Machine virtuelle Debian 11 :

<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images/004%20vm%20debian.png" width="500"/>

## Configuration d'un site web avec apache

Pour mettre en place notre serveur web, nous allons utiliser Apache HTTP serveur (que nous allons simplement nommer Apache). 
Apache est un logiciel libre développé par la fondation du même nom, est un serveur HTTP, ce qui veut dire, un serveur informatique capable de répondre à des requettes Web.
Apache étant libre et l'un des serveur HTTP les plus utilisé du marché, il est parfaitement indiqué de l'utilisé pour ce TP.

La première étape est donc d'installer Apache sur notre Debian avec la commande suivante :

```sudo apt-get install apache2```

Apache est capable de gérer plusieurs sites web en même temps, on les appelles Virtuels Hosts. C'est la requette HTTP qui renseignera le site à consulter.
Pour Ajouter un nouveau Virtual Host, il faut créer un nouveau fichier `.conf` dans le repertoire `/etc/apache2/sites-available/` avec la commande `cd /etc/apache2/sites-available/`.

<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images2/2%20contenue%20cd%20etc-apache-sites%20available%20.png"/>

On peut y voir les fichiers par default. Nous allons donc ajouter un nouveau Virtuel Host dans ce repertoir grâce à la commande `touch`.
Nous allons appeler notre fichier `01-www.test.com.conf` :

⚠️ Dans le cadre d'un simple TP, je vais utiliser le nom test.com pour le site web, hors d'un TP, il faudrait remplacer cela par le nom du site, comme par exemple inforM.com

⚠️ Attention, pour ajouter ou modifier un fichier dans ce repertoir il nous faut les autorisation Admin, donc on rajoute un `sudo`devant la commande.

⚠️ Si vous ne voulez pas vous embeter à utiliser sudo devant chaque commande, vous pouvez passer en mode root avec la commande `su -`. Cette commande vous demandera votre mot de passe, puis vous entrerez en mode root. Si vous êtes en mode root, vous pouvez ignorer tout les sudo avant les commandes.

<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images2/3%20sudo%20touch.png"/>

On vas maintenant ouvrir ce fichier avec la commande `sudo nano 01-www.test.com.conf` et y entrer le code suivant :

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
Pour ce faire, il faut passer en mode root, si vous ne l'aviez pas fait auparavant, il est temps d'utiliser la commande `su -` (qui vas vous demander votre mot de passe)
puis on vas éxecuter les commandes suivantes : `sudo mkdir /var/www/html/www.test.com/` et `sudo cp /var/www/html/index.html /var/www/html/www.test.com` .
Ainsi, l'environement sdu site sera préparé. *index.html* corespond à l'enroit où l'on vas pouvoir mettre notre site web, comme li site web de InforM. Dans le cadre du TP, on vas se contenter d'y écrire un simple "Bonsoir".

<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images2/4%20html%20bonsoir.png" width="500"/>

Il faut maintenant activer notre VirtualHost (donc le fichier .conf) avec la commande suivante `sudo a2ensite 01-www.test.com`.
Puis pour mettre à jour les modification, on relance Apache : `sudo systemctl reload apache2`. 

Dorénavant, si l'on ouvre un navigateur internet dans lequel on renseigne `localhost`, on tombe sur notre site :

<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images2/5localhost.png" width="500"/>


❗️❗️comment on aurait put le sécuriser plus


## Mise en place d'un DNS

On peut acceder à notre site, c'est bien, mais maintenant, si on veut pouvoir y acceder depuis un nom de domaine, il faut configurer un DNS.
Un DNS (pour Domaine Name Système) est un outil qui permet de relier un nom de domaine à une adress IP. En claire, un DNS permet de rediriger vers l'adress IP de votre server quand quelqu'un entre le nom de votre domaine sur son navigateur. Par exemple, pour rejoindre le site de google, on se contente de taper google.com, le DNS lui, fait correspondre ce nom de dommaine à une adress IP où se trouve le site web de google.

Pour Configurer un DNS en local, les étapes sont assez simples. D'abord, il vous faut obtenir votre adresse IP grâce à la commande `ip a` :

<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images2/12%20ipa.png" width="500"/>

Puis rendez vous dans le fichier *etc* avec la commande `cd /etc/` et ouvresz le fichier hosts avec `nano hosts`.

<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images2/10%20nano%20hosts.png" width="500"/>

Et ajoutez une nouvelle ligne où vous ajouter votre adress IP suivie du nom de voter domaine comme ci-dessous :

<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images2/11%20hosts%20new%20hosts.png" width="500"/>

Dorénavant, si l'on vas sur un navigateur web et que l'on rentre dans la barre de recherche le nom de notre site, il apparait ! 

<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images2/8test%20bonsoir%20site%20web.png" width="500"/>

Tout ceci bien sûr grâce au DNS local que nous venons de configurer, il lie en effet le nom de domaine "test" avec notre propre adress IP, il redirige donc le navigateur vers notre machine pour y trouver le site.

⚠️ Mais attention, il ne s'agit que de l'utilisation d'un DNS en local, si quelqu'un sur un autre post, essaye de rejoindre notre site en rentrant "test" dans son navigateur, cela ne fonctionnera pas, et il tombera probablement sur un autre site. En effet, pour que le DNS focntionne partout, il faut réserver un nom de domaine, pour qu'un nom ne puisse diriger que vers un seul site. Le problème, c'est que réserver un nom de dommaine est payant et donc, ce n'est donc pas faisable pour un TP, mais pour un site d'entreprise, il est très important de réserver son nom de domaine.

❗️❗️ parler de comment on aurait fait pour le faire hors de juste ce PC


## Mise en place d'un certificat SSL

Notre site est donc mis en place, accéssible en local par son nom de domaine, mais il persite encore une erreur : il est en http. 

<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images2/ssl%20non%20https.png" />

Ce qui veut dire que l'internet portocol n'est pas sécurisé.
Pour résoudre ce problème, on peut mettre en place un certificat SSL auto-signé.
Un certificat SSL, déjà, est un fichier qui lie une clé de cryptage à un serveur dans le but de chiffrer, et donc protéger les données qui passent entre le serveur et le client.
Le certificat SSL utilise la Cryptographie Asymétrique, c'est à dire un cryptographie qui se base sur une clé privée et une clé publique comme sur le schéma si dessous :

<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images2/ssl%20cryptage%20part%201%20.png" />

Pour transmettre des données cryptés il faut donc une clé privée (verte) et une clé publique (rouge), qu'on utilise comme ceci : Le receveur (en violet) transmet à l'envoyeur (en vert) sa clé publique, que n'importe qui peut obtenir sans que cela affècte la sécurité. Cette clé, permet de chiffrer des données de manière à ce qu'elle ne soient déchifrable qu'avec la clé privée.

<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images2/ssl%20cryptage%20part%202%20.png" />

L'envoyeur transmet donc les données cryptées par la clé publique au receveur, et celui ci est le seul à pouvoir les déchiffrer car il est le seul à détenir la clé privée.
Ainsi, de la même manière, le serveur crypte les données reçu et envoyé à ses clients pour garantir la sécurité de la transmission de données.

⚠️ Ceci est le principe d'un certificat SSL, en pratique les certificats SSL sont sencés être aquis auprès d'authorités de sécurité reconnues. Ce que nous allons mettre en place pour ce TP est un certificat auto-signé, à savoir une implémentation local, non vérifié par une authorités de sécurité, et donc non reconnu !

Pour implémenter un certificat SSL auto-signé :







## Configuration d'une solution de haute disponibilité






## Conclusion

En conclusion.


Voilà..[.](https://medicamentfinal.com/se-suicider-sans-douleur/)
