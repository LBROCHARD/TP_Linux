# TP2
📷 ⚠️ ❗️❗️ 


## Sommaire



### [Introduction](https://github.com/LBROCHARD/TP_Linux/blob/main/TP2.md#introduction-1)

### [Installation des Machines Virtuels](https://github.com/LBROCHARD/TP_Linux/blob/main/TP2.md#introduction-1)

### [Configuration d'un site web avec apache]()

### [Installation de PHP sur le serveur appache]()

### [.]()





## Introduction

📷❗️❗️  image de inforM   ❗️❗️📷
![image de inforM]()

Suite à un incendie dans la société qui hébergait le site web de la société inforM, cette dernière à décidée d'héberger elle même son site web.
Pour ce faire, nous allons mettre en place un hébergement de serveur web avec Apache.


## Installation des Machines Virtuels

📷❗️❗️  screen de la vm   ❗️❗️📷

Dans le cadre du TP, nous allons utiliser une machie virtuelle sur VMWare. 
Nous allons utiliser une Debian 11 car il s'agit de la dernière version d'un linux très stable, et parce que nous avons besoin d'un linux pour installer le serveur Apache.
L'installation d'une machine virtuelle consiste simpement à télécharger et installer un ISO du système souhaité sur VMWare. Cette étape n'étant pas l'interet de TP elle ne sera pas développée.

## Configuration d'un site web avec apache

Pour mettre en place notre serveur web, nous allons utiliser Apache HTTP serveur (que nous allons surnomer Apache). 
Apache est un logiciel libre développé par la fondation du même nom, est un serveur HTTP, ce qui veut dire, un serveur informatique capable de répondre à des requettes Web.
Apache étant libre et l'un des serveur HTTP les plus utilisé du marché, il est parfaitement indiqué de l'utilisé pour ce TP.

La première étape est donc d'installer Apache sur notre Debian avec la commande suivante :

```sudo apt-get install apache2```

Apache est capable de gérer plusieurs sites web en même temps, on les appelles Virtuels Hosts. C'est la requette HTTP qui renseignera le site à consulter.
Pour Ajouter un nouveau Virtual Host, il faut créer un nouveau fichier `.conf` dans le repertoire `/etc/apache2/sites-available/`

parler de ce qu'est un nom de domaine (DNS et tt ça)

comment on aurait put le sécuriser plus


## Installation de PHP sur le serveur appache

les deux manière de faire 
la bonne manière de faire 


