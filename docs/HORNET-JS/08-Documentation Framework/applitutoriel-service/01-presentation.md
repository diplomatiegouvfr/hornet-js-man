# Applitutoriel-service

applitutoriel-service est la partie service (JEE) de l'application exemple basée sur Hornet 5. 

## Deploiement serveur

### Pré-requis

- tomcat 8.X
- maven 3.3.9
- openJdk 8
- hornet-service 5.1.X 

### Installation

- Récupérer le projet applitutoriel-service
- Lancer la commande de construction 

``` shell
mvn package
```

- Dézipper les fichiers de configuration présents dans l'archive `applitutoriel-service-5.1.X-environment.zip`.
- Déployer l'application dans un conteneur Tomcat 8, en prenant garde que la variable `conf/applitutorielprop` du fichier `context.xml` de l'application, référence bien le chemin des fichiers de configuration dézippés à utiliser suivant l'environnement. 

### Vérification

Pour cela, il suffit de faire appel à un service REST depuis un navigateur exemple : `http://localhost:8080/applitutoriel-service/produits`

## Démarrage Spring Boot poste de Dev

### Pré-requis

- maven 3.3.9
- openJdk 8
- hornet-service 5.1.X 
- Eclispe
### Installation

- Récupérer le projet applitutoriel-service
- Ajouter la ligne suivante dans le fichier `eclipse.ini` pour activer l'annotation processing avec Lombok :

```
-javaagent:/home/mouliwi/.m2/repository/org/projectlombok/lombok/1.16.18/lombok-1.16.18.jar

```
- Sous votre IDE, importer le projet maven
- Lancer la commande d'installation des dépendances avec l'IDE ou la commande

``` shell
mvn install -Pdev-spring-boot
```


- Lancer la classe `fr.gouv.diplomatie.applitutoriel.Application` comme Java Application. Cette classe est le point d'entrée. Un serveur Web sera lancé

ou 

``` shell
mvn spring-boot:run -Pdev-spring-boot
```

### Vérification

Pour cela, il suffit de faire appel à un service REST depuis un navigateur exemple : `http://localhost:8080/applitutoriel-service/produits`

### Customisation

Le port et le context path peuvent être modifier et sont configuré dans le fichier `application.properties`
