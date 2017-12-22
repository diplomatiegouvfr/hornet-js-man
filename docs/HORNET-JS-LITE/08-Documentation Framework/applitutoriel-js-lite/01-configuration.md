# Configuration

## Fichier de configuration de l'application : default.json

L'ensemble de la configuration applicative du serveur NodeJS se situe dans le fichier default.json et log4js.json contenus dans les sources de l'application.

La configuration des logs serveur a été séparée du reste et se trouve dans le fichier dédié log4j.json : cf chapitre ["Fichier de configuration des logs serveur de l'application"](/hornetshowroom/composant/page/hornet-js/documentation_framework/applitutoriel-js/configuration).

La configuration est commune à une application Hornet classique, hormis la configuration liée à la connexion d'une base de données.

## Configuration d'une base de données

Il est possible d'ajouter une configuration permettant de se connecter à une base de données.

Les configurations de base de données déclarés dans la configuration peuvent ensuite être utilisées dans une classe implémentant `IModelDAO` du projet `hornet-js-database`.

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
|database|Object contenant la liste des bases de données et leur configuration||
|configApplituto| Nom de la base de données avec sa configuration||
|uri|Chaine de connexion à une bdd [sgbd]://[user]@[host]:[ports]/[name]||
|options|Options nécessaires ou facultatives pour le sgbd||
|reload|Indicateur pour exécution des scripts sql à chaque démarrage|false par défaut

```json
    "database": {
        "configApplituto": {
          "uri": "postgres://hornetJS@localhost:5433/applitutorielDB",
          "options" : {
            "pool" : {
              "max" : 5,
              "min" : 0,
              "idle" : 1000
            }
          }
        }
      }
```
