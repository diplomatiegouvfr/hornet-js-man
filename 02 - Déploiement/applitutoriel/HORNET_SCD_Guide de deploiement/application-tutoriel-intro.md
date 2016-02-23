# Introduction

## Présentation

L'application « Tutoriel » a pour objectif de présenter une application basée sur le framework Hornet.

L'application est scindée en deux modules :

- applitutoriel-service (Partie JAVA/Tomcat)
- applitutoriel-js (Partie Javascript/Nodejs)

L’infrastructure nécessaire au bon fonctionnement du projet (systèmes d’exploitation, serveur Web, serveur d’application, JRE à utiliser, du SGBD…) est celle de la filière Hornet.

# Liste des livrables

Ce chapitre décrit les éléments issus de la construction des parties client et serveur.

## Packaging 

Le packaging de l’applitutoriel-service est prévu pour un déploiement sur un serveur d’application Apache Tomcat (délivrant la partie dynamique).

Le packaging de l’applitutoriel-js est prévu pour un déploiement sur un serveur d’application NodeJS (délivrant la partie dynamique) et sur un frontal web Apache httpd (délivrant la partie statique). 

Le serveur de thèmes mutualisé hornet-themes est livré séparément sur un CDN. (Voir HORNET_SCD_Guide de déploiement des Themes)

## Liste des livrables

- applitutoriel-5.0.X-dynamic.zip 
- applitutoriel-5.0.X-static.zip 

- applitutoriel-service-5.0.X-config.zip
- applitutoriel-service-5.0.X-context.zip
- applitutoriel-service-5.0.X.war

# Configuration pré-déploiement

Ce chapitre décrit les étapes de configuration à effectuer avant le déploiement de l’application applitutoriel.

