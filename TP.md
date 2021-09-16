# TP Linux n°1

***/!\ METTRE UNE PHOTO POUR FAIRE LA PAGE DE GARDE /!\\***

## Sommaire

- ### [Introduction](https://github.com/LBROCHARD/TP_Linux/blob/main/TP.md#introduction-1)
- ### [Mise en place des machines virtuelles](https://github.com/LBROCHARD/TP_Linux/blob/main/TP.md#mise-en-place-des-machines-virtuelles-1)
- ### [Configuration des services réseaux](https://github.com/LBROCHARD/TP_Linux/blob/main/TP.md#configuration-des-services-r%C3%A9seaux-1)
- ### [Configuration de l'outil de gestion de ticket]()
- ### [Ajout d'un plugin de remonté de poste client]()
- ### [Mise en place d'une sauvegarde du GLPI]()

## Introduction

Le but de ce TP est la mise en place d'un parc informatique et d'un logiciel de ticketing pour permettre à la société **InforM** d'effectuer sa tâche de formation et d'entretien du materiel pour leurs clients.
Nous allons donc mettre un place un serveur GLPI qui vas permettre à la société de faire ces deux choses en même temps et sur un seul outil.
![](https://github.com/LBROCHARD/TP_Linux/blob/main/images/002%20infoM.png)

## Mise en place des machines virtuelles

La Première étape est l'étape d'installation des VM (car il s'agit d'un TP), dans notre cas, on a besoin d'une machine sous Linux pour habriter le serveur GLPI, et d'une machine sous Windows pour émuler la machine d'un client. 
Pour la machine Linux, on vas utiliser un Debian 11, et pour la machine client, on vas simplement utiliser un windows 10. 
Leur mise en place est assez simple, on télécharge simplement des iso des different systèmes pour les installer sur VM ware, c'est une étape que l'on ne vas pas détailler.
Machine Debian :
![Machine Debian](https://github.com/LBROCHARD/TP_Linux/blob/main/images/004%20vm%20debian.png)
Machine Windows :
![Machine Windows](https://github.com/LBROCHARD/TP_Linux/blob/main/images/004%20vm%20windows.png)

## Configuration des services réseaux

Par défaut sur VMware le réseau est en mode NAT, c'est à dire que le réseau de la machine virtuelle, agit comme si il s'agissait du réseau de l'ordinateur hôte.
Il conviens donc de passer le réseau en "Bridged" (réseau par pont) qui permet à la machine virtuelle d'agir comme si elle était une machine à part entière sur le même réseau que l'hôte, ce qui vas nous permettre de simuler un réseau dans lequel avec un ordinateur serveur (la VM débian) et un ordinateur client (la VM windows).
Pour ce faire, il suffit d'aller dans les réglages de la machine virtuelle, et de mofifier dans "Network Adaptater" le réseau de "NAT" à "Bridged" comme ci-dessous :
![](https://github.com/LBROCHARD/TP_Linux/blob/main/images/001%20passage%20de%20la%20debian%20en%20bridge.png)
Un soucis rencontré fut la présence d'une sécurité dur le réseau d'Ynov, qui empechait au reseau de fonctionner sur une machine virtuelle configuré en bridged, pour le régler, nous nous sommes connecté au réseau d'un téléphone.

Sur le réseau du téléphone :
![sur le reseau du téléphone](https://github.com/LBROCHARD/TP_Linux/blob/main/images/003%20connection.png)
Sur le réseau d'Ynov :
![sur le reseau d'Ynov](https://github.com/LBROCHARD/TP_Linux/blob/main/images/003%20connection%20no.png) 
![sur le reseau d'Ynov](https://github.com/LBROCHARD/TP_Linux/blob/main/images/003%20connection%20echec.png) 
