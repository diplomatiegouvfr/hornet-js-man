
# applitutoriel-service

applitutoriel-service est la partie service (JEE) de l'application exemple basée sur Hornet 5. 

## Pré-requis

- tomcat 8.X
- maven 3.2.5
- openJdk 8
- hornet-service 5.X 

## Installation

- Récupérer le projet applitutoriel-service
- Lancer la commande de construction 

``` shell
mvn package
```

- Dézipper les fichiers de configuration présents dans l'archive `applitutoriel-service-5.0.X-config.zip`.
- Déployer l'application dans un conteneur Tomcat 8, en prenant garde que la variable `conf/applitutorielprop` du fichier `context.xml` de l'application, référence bien le chemin des fichiers de configuration dézippés. 

## Vérification

Pour celà, il suffit de faire appel à un service REST depuis un navigateur exemple : `http://localhost:8080/applitutoriel-service/secteurs`

## Configuration de l'application