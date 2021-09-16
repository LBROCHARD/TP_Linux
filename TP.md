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

## Mise en place des machines virtuelles

La Première étape est l'étape d'installation des VM (car il s'agit d'un TP), dans notre cas, on a besoin d'une machine sous Linux pour habriter le serveur GLPI, et d'une machine sous Windows pour émuler la machine d'un client. 
Pour la machine Linux, on vas utiliser un Debian 11, et pour la machine client, on vas simplement utiliser un windows 10. 
Leur mise en place est assez simple, on télécharge simplement des iso des different systèmes pour les installer sur VM ware, c'est une étape que l'on ne vas pas détailler.

## Configuration des services réseaux

Par défaut sur VMware le réseau est en mode NAT, c'est à dire que le réseau de la machine virtuelle, agit comme si il s'agissait du réseau de l'ordinateur hôte 
