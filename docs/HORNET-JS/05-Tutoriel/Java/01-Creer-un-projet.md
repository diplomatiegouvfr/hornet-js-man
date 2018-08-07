# Création du projet service à partir d'un archetype Maven


Pré-requis : 
Configurer le settings.xml maven pour pointer vers le repo Hornet contenant le catalogue.

Exemple de catalogue hornet à positionner dans un repo maven contenant hornet si besoin : `archetype-catalog.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<archetype-catalog xmlns="http://maven.apache.org/plugins/maven-archetype-plugin/archetype-catalog/1.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/plugins/maven-archetype-plugin/archetype-catalog/1.0.0                                        http://maven.apache.org/xsd/archetype-catalog-1.0.0.xsd">
    <archetypes>
        <archetype>
            <groupId>fr.gouv.diplomatie.hornet</groupId>
            <artifactId>hornet-service-archetype</artifactId>
            <version>5.x.x</version>
            <repository>http://[URL_REPO_MAVEN]/repository>
            <description>Archetype hornet-service</description>
        </archetype>
    </archetypes>
</archetype-catalog>
```

Se positionner dans le répertoire du projet et exécuter la commande suivante :

```shell
mvn archetype:generate -DarchetypeGroupId=fr.gouv.diplomatie.hornet -DarchetypeArtifactId=archetype-hornet-service -DarchetypeVersion=5.x.x
```

| Information | Valeur à saisir                  |
|------------ | -------------------------------- | 
| groupId     | fr.gouv.diplomatie.applitutoriel |
| artifactId  | applitutoriel                    |
| version     | 1.0-SNAPSHOT                     |
| package     | fr.gouv.diplomatie.applitutoriel |

## Import du projet Maven dans Eclipse

Depuis l'arborescence projet, faites un clic-droit puis sélectionnez `Import...`, ou via le menu `File > Import...`

Dans la fenêtre ouverte choisissez `Maven > Existing Maven Project`

Indiquez l'emplacement de votre projet, puis cliquez sur `Finish`

Votre projet apparaît alors dans l'arborescence des projets

## Démarrer les services

### Configurer Eclipse pour utiliser Tomcat 8

Allez dans `Window > Preferences`, puis `Server > Runtime Environments`. Cliquez sur `Add...`. Dans le répertoire `Apache` sélectionnez `Apache Tomcat v8.0`. Dans `Tomcat installation directory` renseignez votre répertoire contenant les sources apache-tomcat :

```
/home/votreuser/Dev/apache-tomcat-8.5.X
```

Cliquez sur `Finish` puis `OK`.

### Ajouter un server Tomcat 8 et publiez le projet

Cliquez sur l'onglet `Servers` puis `No servers are available. Click this link to create a new server...`

Choisissez `Tomcat v8.5 Server`, conservez les valeurs par défauts et cliquez sur `Finish`

Faites un clic droit sur votre serveur puis `Add and Remove...` Sélectionnez votre application de services et `Add >` puis `Finish`

### Démarrer le serveur

Vous pouvez lancer le serveur en allant dans la vue `servers`.
Clicquez droit sur le serveur puis cliquez sur `start`.
