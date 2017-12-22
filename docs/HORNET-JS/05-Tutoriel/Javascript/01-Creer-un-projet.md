# Création d'un projet Hornet-js

Ce document présente les étapes de création d’un projet Hornet 5.1 ainsi que l’intégration de nouveaux services au sein d’une application Hornet.

## Création du projet js à partir d'un template yeoman

Installer Yeoman :

```shell
    npm install -g yo
```

créer un lien symbolique vers Yeoman dans $HOME/bin :

```shell
    ln -s /home/votreuser/Dev/node-v6.10.X-linux-x64/lib/node_module/yo/lib/cli.js
```

Installer le template Yeoman pour un projet hornet-js :

```shell
    npm install -g generator-hornet-js
```

Créer un répertoire pour l'application, s'y positionner et lancer la génération d'un nouveau projet hornet-js :

```shell
    mkdir applitutoriel-js
    cd applitutoriel-js
```

```shell
    yo hornet-js
```

| Information | Valeur par défaut | Valeur à saisir  |
|---------- | ---- | ----------- |
| Nom de votre projet | - | applitutoriel-js |
| Version de votre projet | 1.0.0 | 1.0.0-rc.1 |
| Description de votre projet | - | application tutoriel |
| Version du framework (hornet-js) | - | 5.1.0 |
| Thême de l'application | - | hornet-themes-intranet |
| Version du thême de l'application | - | 5.1.0 |
| Host de la partie service | - | http://localhost:8080 |
| ContextPath de la partie service | - | applitutoriel |
| Mode full spa | - | false |

## Création du projet service à partir d'un archetype Maven

Se positionner dans le répertoire du projet et exécuter la commande suivante :

```shell
    mvn archetype:generate -DarchetypeGroupId=fr.gouv.diplomatie.hornet -DarchetypeArtifactId=hornet-service-archetype -DarchetypeVersion=5.1.0
```

| Information | Valeur à saisir  |
|---------- | ---- | ----------- |
| groupId | fr.gouv.diplomatie.applitutoriel |
| artifactId | applitutoriel |
| version | 1.0-SNAPSHOT |
| package | fr.gouv.diplomatie.applitutoriel |

## Import du projet Maven dans Eclipse

Depuis l'arborescence projet, faites un clic-droit puis sélectionnez `Import...`, ou via le menu `File > Import...`

Dans la fenêtre ouverte choisissez `Maven > Existing Maven Project`

Indiquez l'emplacement de votre projet, puis cliquez sur `Finish`

Votre projet apparaît alors dans l'arborescence des projets

## Démarrer les services

### Configurer Eclipse pour utiliser Tomcat 8

Allez dans `Window > Preferences`, puis `Server > Runtime Environments`. Cliquez sur `Add...`. Dans le répertoire `Apache` sélectionnez `Apache Tomcat v8.0`. Dans `Tomcat installation directory` renseignez votre répertoire contenant les sources apache-tomcat :

```
    /home/votreuser/Dev/apache-tomcat-8.0.29
```

Cliquez sur `Finish` puis `OK`.

### Ajouter un server Tomcat 8 et publiez le projet

Cliquez sur l'onglet `Servers` puis `No servers are available. Click this link to create a new server...`

Choisissez `Tomcat v8.0 Server`, conservez les valeurs par défauts et cliquez sur `Finish`

Faites un clic droit sur votre serveur puis `Add and Remove...` Sélectionnez votre application de services et `Add >` puis `Finish`

### Démarrer le serveur

Vous pouvez lancer le serveur en allant dans la vue `servers`.
Clicquez droit sur le serveur puis cliquez sur `start`.

## Démarrer l'application

Positionnez vous dans le répertoire de l'application Hornet générée précédemment et lancez l'installation :

```shell
    hb install
```

Une fois l'installation terminée, compilez les sources :

```shell
    hb compile
```

L'application est prête à être démarrée :

```shell
    hb w -i
```