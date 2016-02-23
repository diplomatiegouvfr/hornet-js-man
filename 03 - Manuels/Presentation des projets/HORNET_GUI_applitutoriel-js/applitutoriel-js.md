# applitutoriel-js

L'applitutoriel a pour objectif de présenter une application basée sur le framework Hornet.

A noter :
* Hornet facilite la mise en oeuvre du RGAA V3 dans une application.
* Mais l'utilisation de Hornet ne garantit pas qu'une application soit valide RGAA.

__Cas fonctionnels__

Les cas fonctionnels présentés dans l'application sont :

* Formulaire de recherche
* Présentation du résultat sous forme de tableau éditable
* Formulaire étendu
* Tableau d'ajout/suppression/modification d'items
* Affichage de graphique

__RGAA V3__

L'applitutoriel est une mise en pratique du RGAA V3 au travers du framework Hornet.

## Prérequis #

* NodeJS 4.X
* hornet-js-builder 1.X installé en global:

```shell
    $ npm install -g hornet-js-builder
```

* checkout du projet `applitutoriel-js`

## Initialisation #
Se positionner dans le répertoire du projet `applitutoriel-js` et lancer la commande:

```shell
    $ hb install
```

## Démarrage de l'application en mode développement #

### Commande par défaut

la commande à exécuter en mode développement est la suivante:

```shell
    $ hb w
```

Elle permet de lancer l'application en mode `watcher` afin que les modifications soient prises en compte (ce qui
entrainera un redémarrage du serveur node dans le cas d'une détection de modification).

### Options

Il est également possible d'ajouter à cette commande l'option:

```shell
    $ hb w -i
```

Cette commande indique au builder de ne pas transpiler les fichiers typescript en javascript.
Elle est à utiliser dans le cas où l'IDE a été configuré de telle sorte que la transpilation ts->js
se fasse via ce dernier.


## Vérification

L'application est accessible depuis un navigateur à l'addresse : `http://localhost:8888/applitutoriel/`
où __applitutoriel__ correspond au `contextPath` dans le fichier `config/default.json`.  

## Mode Mock

Il est possible d'utiliser l'applitutoriel sans déployer la partie service.
Pour cela, activer le mode `mock` dans le `config/default.json`.

```json
  "mock": {
    "enabled": true,
    "host": "127.0.0.1", //default localhost
    "routes": "/mock/routes"
  }
```

## Packaging de l'application

```shell
$ hb package
```

Les livrables sont à récupérer dans le répertoire : `target`

- `applitutoriel-5.0.X-static.zip`
- `applitutoriel-5.0.X-dynamic.zip`

## Configuration de l'application
