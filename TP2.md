# Linux TP n°2


<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images2/linux2.png" />width="500"


## Sommaire



- ### [Introduction](https://github.com/LBROCHARD/TP_Linux/blob/main/TP2.md#introduction-1)

- ### [Installation des Machines Virtuels](https://github.com/LBROCHARD/TP_Linux/blob/main/TP2.md#installation-des-machines-virtuels-1)

- ### [Configuration d'un site web avec apache](https://github.com/LBROCHARD/TP_Linux/blob/main/TP2.md#configuration-dun-site-web-avec-apache-1)

- ### [Mise en place d'un DNS](https://github.com/LBROCHARD/TP_Linux/blob/main/TP2.md#mise-en-place-dun-dns-1)

- ### [Mise en place d'un certificat SSL](https://github.com/LBROCHARD/TP_Linux/blob/main/TP2.md#mise-en-place-dun-certificat-ssl-1)

- ### [Configuration d'une solution de haute disponibilité](https://github.com/LBROCHARD/TP_Linux/blob/main/TP2.md#configuration-dune-solution-de-haute-disponibilit%C3%A9-1)

- ### [Conclusion](https://github.com/LBROCHARD/TP_Linux/blob/main/TP2.md#conclusion-1)




## Introduction

![](https://github.com/LBROCHARD/TP_Linux/blob/main/images/002%20infoM.png)

Suite à un incendie dans la société qui hébergeait le site web de la société inforM, cette dernière a décidée d'héberger elle-même son site web.
Pour ce faire, nous allons mettre en place un hébergement de serveur web avec Apache, puis configurer un DNS, et mettre en place un certificat SSl.



## Installation des Machines Virtuels

Dans le cadre du TP, nous allons utiliser une machine virtuelle sur VMWare. 
Nous allons utiliser une Debian 11 car il s'agit de la dernière version d'un linux très stable, et parce que nous avons besoin d'un linux pour installer le serveur Apache.
L'installation d'une machine virtuelle consiste simplement à télécharger et installer un ISO du système souhaité sur VMWare. Cette étape n'étant pas l'intérêt de ce TP elle ne sera pas développée.

La Machine virtuelle Debian 11 :

<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images/004%20vm%20debian.png" width="500"/>

## Configuration d'un site web avec apache

Pour mettre en place notre serveur web, nous allons utiliser Apache HTTP serveur (que nous allons simplement nommer Apache). 
Apache est un logiciel libre développé par la fondation du même nom, est un serveur HTTP, ce qui veut dire, un serveur informatique capable de répondre à des requêtes Web.
Apache étant libre et l'un des serveurs HTTP les plus utilisé du marché, il est parfaitement indiqué de l'utiliser pour ce TP.

La première étape est donc d'installer Apache sur notre Debian avec la commande suivante :

```sudo apt-get install apache2```

Apache est capable de gérer plusieurs sites web en même temps, on les appelle Virtuels Hosts. C'est la requête  HTTP qui renseignera le site à consulter.
Pour Ajouter un nouveau Virtual Host, il faut créer un nouveau fichier `.conf` dans le repertoire `/etc/apache2/sites-available/` avec la commande `cd /etc/apache2/sites-available/`.

<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images2/2%20contenue%20cd%20etc-apache-sites%20available%20.png"/>

On peut y voir les fichiers par défaut. Nous allons donc ajouter un nouveau Virtuel Host dans ce répertoire grâce à la commande `touch`.
Nous allons appeler notre fichier `01-www.test.com.conf` :

⚠️ Dans le cadre d'un simple TP, je vais utiliser le nom test.com pour le site web, hors d'un TP, il faudrait remplacer cela par le nom du site, comme par exemple inforM.com

⚠️ Attention, pour ajouter ou modifier un fichier dans ce répertoire il nous faut les autorisations Admin, donc on rajoute un `sudo`devant la commande.

⚠️ Si vous ne voulez pas vous embêter à utiliser sudo devant chaque commande, vous pouvez passer en mode root avec la commande `su -`. Cette commande vous demandera votre mot de passe, puis vous entrerez en mode root. Si vous êtes en mode root, vous pouvez ignorer tous les sudo avant les commandes.

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
Le *ServerName* correspond au nom utilisé par le Virtuel Host sur notre serveur, il ne peut y avoir qu'un seul *ServerName* mais il peut y avoir plusieurs *ServerAlias* (qui correspondent à des noms alternatifs au cas où le *ServerName* ne fonctionne pas).
Le *ServerAdmin* correspond à l'adresse mail qui sera utilisée sur certaines erreurs pour entrer en contact avec le modérateur.
Et enfin le *DocumentRoot* est l'emplacement des fichiers de votre site web.

⚠️ Il existe encore de nombreux paramètres que nous aurions pu régler, mais dans le cadre d'un TP, cette configuration suffira.

Maintenant que notre VirtualHost a été créé, il nous reste donc a implémenter notre site web.
Pour ce faire, il faut passer en mode root, si vous ne l'aviez pas fait auparavant, il est temps d'utiliser la commande `su -` (qui va vous demander votre mot de passe)
puis on va exécuter les commandes suivantes : `sudo mkdir /var/www/html/www.test.com/` et `sudo cp /var/www/html/index.html /var/www/html/www.test.com` .
Ainsi, l'environnement du site sera préparé. *index.html* correspond à l'enroit où l'on va pouvoir mettre notre site web, comme li site web de InforM. Dans le cadre du TP, on vas se contenter d'y écrire un simple "Bonsoir".

<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images2/4%20html%20bonsoir.png" width="500"/>

Il faut maintenant activer notre VirtualHost (donc le fichier .conf) avec la commande suivante `sudo a2ensite 01-www.test.com`.
Puis pour mettre à jour les modifications, on relance Apache : `sudo systemctl reload apache2`. 

Dorénavant, si l'on ouvre un navigateur internet dans lequel on renseigne `localhost`, on tombe sur notre site :

<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images2/5localhost.png" width="500"/>


## Mise en place d'un DNS

On peut accéder à notre site, c'est bien, mais maintenant, si on veut pouvoir y accéder depuis un nom de domaine, il faut configurer un DNS.
Un DNS (pour Domaine Name Système) est un outil qui permet de relier un nom de domaine à une adresse IP. En clair, un DNS permet de rediriger vers l'adresse IP de votre serveur quand quelqu'un entre le nom de votre domaine sur son navigateur. Par exemple, pour rejoindre le site de google, on se contente de taper google.com, le DNS lui, fait correspondre ce nom de domaine à une adresss IP où se trouve le site web de google.

Pour Configurer un DNS en local, les étapes sont assez simples. D'abord, il vous faut obtenir votre adresse IP grâce à la commande `ip a` :

<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images2/12%20ipa.png" width="500"/>

Puis rendez vous dans le fichier *etc* avec la commande `cd /etc/` et ouvrez le fichier hosts avec `nano hosts`.

<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images2/10%20nano%20hosts.png" width="500"/>

Et ajoutez une nouvelle ligne où vous ajoutez votre adresse IP suivie du nom de voter domaine comme ci-dessous :

<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images2/11%20hosts%20new%20hosts.png" width="500"/>

Dorénavant, si l'on va sur un navigateur web et que l'on rentre dans la barre de recherche le nom de notre site, il apparaît ! 

<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images2/8test%20bonsoir%20site%20web.png" width="500"/>

Tout ceci bien sûr grâce au DNS local que nous venons de configurer, il lie en effet le nom de domaine "test" avec notre propre adresse IP, il redirige donc le navigateur vers notre machine pour y trouver le site.

⚠️ Mais attention, il ne s'agit que de l'utilisation d'un DNS en local, si quelqu'un sur un autre post, essaye de rejoindre notre site en rentrant "test" dans son navigateur, cela ne fonctionnera pas, et il tombera probablement sur un autre site. En effet, pour que le DNS fonctionne partout, il faut réserver un nom de domaine, pour qu'un nom ne puisse diriger que vers un seul site. Le problème, c'est que réserver un nom de domaine est payant et donc, ce n'est donc pas faisable pour un TP, mais pour un site d'entreprise, il est très important de réserver son nom de domaine.


## Mise en place d'un certificat SSL

Notre site est donc mis en place, accessible en local par son nom de domaine, mais il persiste encore une erreur : il est en http. 

<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images2/ssl%20non%20https.png" />

Ce qui veut dire que l'internet protocol n'est pas sécurisé.
Pour résoudre ce problème, on peut mettre en place un certificat SSL auto-signé.
Un certificat SSL, déjà, est un fichier qui lie une clé de cryptage à un serveur dans le but de chiffrer, et donc protéger les données qui passent entre le serveur et le client.
Le certificat SSL utilise la Cryptographie Asymétrique, c'est à dire une cryptographie qui se base sur une clé privée et une clé publique comme sur le schéma ci-dessous :

<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images2/ssl%20cryptage%20part%201%20.png" />

Pour transmettre des données cryptées il faut donc une clé privée (verte) et une clé publique (rouge), qu'on utilise comme ceci : Le receveur (en violet) transmet à l'envoyeur (en vert) sa clé publique, que n'importe qui peut obtenir sans que cela affecte la sécurité. Cette clé, permet de chiffrer des données de manière à ce qu'elles ne soient déchifrable qu'avec la clé privée.

<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images2/ssl%20cryptage%20part%202%20.png" />

L'envoyeur transmet donc les données cryptées par la clé publique au receveur, et celui ci est le seul à pouvoir les déchiffrer car il est le seul à détenir la clé privée.
Ainsi, de la même manière, le serveur crypte les données reçues et envoyées à ses clients pour garantir la sécurité de la transmission de données.

⚠️ Ceci est le principe d'un certificat SSL, en pratique les certificats SSL sont sensés être acquis auprès d'autorités de sécurité reconnues. Ce que nous allons mettre en place pour ce TP est un certificat auto-signé, à savoir une implémentation local, non vérifié par une autorité de sécurité, et donc non reconnu !

Pour implémenter un certificat SSL auto-signé la première étape est d'activer *mod ssl* qui est une fonctionnalité d'apache qui permet de crypter des données. Pour l'activer on utilise la commande `sudo a2enmod ssl`.
Juste après, on redémarre apache avec `sudo systemctl restart apache2`.
Le module mod_ssl est maintenant activé et prêt à l'emploi !

Maintenant, nous allons générer un nouveau certificat SSL. 
Ce certificat mettra en place les clés publiques et privées utilisées pour crypter les données.
Pour créer les fichiers de clés, on utilisa la commande : `sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/apache-selfsigned.key -out /etc/ssl/certs/apache-selfsigned.crt`.

Cette commande contient de nombreuses informations que je vais essayer d'expliquer au mieux :

- Tout d'abord, *openssl* est un outil libre qui nous permet de créer des clés dans le but de crypter nos données (comme expliqué plus haut).
- *req -x509* corresponds au type de format de clé que l'on souhaite créer, ici, le format X509 est un le format le plus utilisé pour le SSL.
- *-nodes* permets à apache d'accéder à cette clé sans accord de l'utilisateur.
- *-days 365* corresponds au temps durant lequel ce certificat sera valide. En effet, pour que les certificats soient changés régulierement (dans un souci de sécurité), la plupart des navigateurs ne reconnaissent pas les certificats dont la période de validité est supérieure à un an (soit 365 jours).
- *-newkey rsa:2048* permet de créer à cette étape une clé RSA de 2048 bits (il s'agit d'une option car la clé aurait pu être créée dans une étape précédente.
- *-keyout* corresponds à l'emplacement de la clé que nous sommes en train de créer.
- et *-out* est l'emplacement ou sera placé le certificat que nous sommes en train de créer.

Suite à cela, il vous est demandé de renseigner certaines informations à propos du site :

- le *Country Name*, initiales du pays d'hébergement (ici "FR").
- *State or Province Name* nom de l'état ou du département (ici "Haute-Garonne").
- *Locality Name* nom de la ville (ici "Toulouse").
- *Organization Name* nom de l'organisation (ici "InforM").
- *Organizational Unit Name* département de l'organisation (ici "." pour laisser blanc).
- *Common Name* il s'agit de votre adresse IP, ou (et comme c'est le cas ici) du nom de votre domaine. ⚠️attention celui-ci est important car il peut apporter de nombreux problèmes de sécurité en cas d'erreur ! (ici "www.test.com").
- *Email Address* corresponds à l'adresse d'un modérateur du serveur, elle permet de contacter un responsable en cas d'erreur (ici laissé blanc dans le cadre du TP).
 
⚠️ tout ce qui a été remplis avec le *ici* correspond aux informations entrées dans le cadre du TP ! Dans un cadre proffesionnel il ne s'agit pas forcément des mêmes valeurs.

On va maintenant mettre à jour notre fichier de configuration avec la commande `sudo nano /etc/apache2/sites-availabe/01-www.test.com.conf` et y rajouter les lignes suivantes : 

<pre>
<code>
   SSLEngine on
   SSLCertificateFile /etc/ssl/certs/apache-selfsigned.crt
   SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key
</code>
</pre>

Et remplacer le *80* de `<VirtualHost *:80>` en *443*.

Cela devrait nous procurer le résultat suivant :

<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images2/ssl%202%20new%20.conf.png" width="500"/>

On relance apache : `sudo systemctl reload apache2`. On se rend sur `https://test` et le message suivant apparaît : 

<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images2/ssl%20https%20attention.png" width="500"/> 

On clique sur *Avancé* puis *Continuer quand même* et on arrive enfin sur notre page, fonctionnelle, en https !

<img src="https://github.com/LBROCHARD/TP_Linux/blob/main/images2/ssl%20https%20qui%20marche.png" width="500"/>

Le certificat SSL auto-signé est donc fonctionnel !

⚠️ Attention tout de même ! Cette méthode ne fonctionne que pour les TP, en effet, on peut voir que les navigateurs mettent en garde contre les certificats auto-signés, donc cette solution ne fonctionnera pas en entreprise.



## Configuration d'une solution de haute disponibilité

La Haute Disponibilité (ou HA) consiste à mettre en place des outils pour assurer la disponibilité d'un serveur le plus souvent possible. En effet parfois, il est important que le serveur reste accessible, et il faut donc prévoir des solutions pour contrer les éventuels problèmes.

La première de ses solution en HA consiste en la Répartition des Charges (ou Load Balancing). Elle consiste à rediriger les requêtes des internautes vers un serveur moins surchargé afin d'enlever de la charge au premier serveur et donc éviter des potentielles erreurs. Mais cette solution s'applique à de grands serveurs, avec de nombreux sous-serveurs capables de s'occupper d'une requette, et cette solution est dificilement envisagable dans ce TP.

La deuxième solution est ce qu'on appelle la Tolérance aux Pannes (FailOver) qui consiste à configurer un second serveur, qui surveille en permanence le serveur principal, et qui peut s'activer si le premier ne fonctionne plus, dans le but de prendre ça place. Ainsi, si le premier serveur ne fonctionne plus, le second prend sa place le temps qu'on règle les soucis sur le premier. Cela peut être très pratique pour eviter les pannes ou autres erreurs.

La Tolérance aux Pannes est la méthode à adopter dans le cadre de ce TP. Pour ce faire, on peut utiliser les outils *Corosync* et *Peacemaker*. 
Corosync est un logiciel qui communiquera entre les 2 serveurs et qui permettra de détecter si le premier serveur ne fonctionne plus. 
Peacemaker quant à lui est un logiciel de gestion de ressources, il pourra donc arrêter ou relancer les services du serveur "de secours" en cas de besoin.

Il faudra donc mettre en place une seconde machine virtuelle avec une copie du serveur, et installer les outils Corosync et Peacemaker sur les machines.


## Conclusion

En conclusion, rapatrier ses serveurs ne consiste pas uniquement à configurer un serveur, car il faut également prendre en compte la publication du site, son accessibilité grâce au DNS, sa sécurité grâce au certificat SSL et son maintien grâce à la Haute Disponibilité.

Ce TP nous aura fait prendre connaissance de tous les outils à mettre en place durant la configuration d'un serveur, ainsi que comment les installer et utiliser. 

En axes d'amélioration, j'aurais probablement dû parler un petit peu plus de toutes les solutions qui auraient dû être mis en place au sein d'une entreprise plutôt que de me contenter de simplement écrire que ce que nous faisions dans le cadre d'un TP n'était pas suffisant. Je regrette également de n'avoir pas pu terminer l'installation de la Haute disponibilité.

Voilà..[.](https://medicamentfinal.com/se-suicider-sans-douleur/)
