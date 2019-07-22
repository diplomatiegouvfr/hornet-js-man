# Node #

Ce document a pour but de décrire les opérations à effectuer pour installer et configurer un environnement de développement pour `hornet-js` version `5.x.x` et supérieur.

## Composition de l’environnement ##

L’environnement d’exécution Hornet est composé des éléments suivants :

- `NodeJS 10 LTS`

L’environnement de développement intégré (IDE) retenu est `Webstorm 2017.3.4`. Il est aussi possible d'utiliser Visual Studio Code.

## Configuration de l'environnement d'exécution ##

La version de NodeJS supportée par Hornet est la `10 LTS`

### Installation de NodeJS ###

Dans un terminal, taper la commande suivante:
```
sudo dpkg -i nodejs-10.16.0.deb
```

### Paramétrage des installations de package globaux ###

1. Créer un répertoire pour les installations globales:

```
mkdir ~/.npm-global

```

2. Configurer NPM pour utiliser le nouveau chemin du répertoire

```
npm config set prefix '~/.npm-global'

```
3. Ouvrir ou créer un fichier `~/.profile` et ajouter cette ligne (à adapter selon le shell utilisé):

```
export PATH=~/.npm-global/bin:$PATH
```

4. de retour sur la ligne de commande, mettre à jour les variables systèmes : 
```
 source ~/.profile
```


### Vérifier NodeJS ###

- Ouvrir une console
- Taper `node --version`
- Doit s'afficher en retour : `v10.16.0`

### Vérifier NPM ###

- Ouvrir une console
- Taper `npm --version`
- Doit s'afficher en retour : `6.9.0`

#### Configuration NPM

A la racine de votre répertoire utilisateur (~/) , modifier ou créer le fichier .npmrc,
 en ajoutant les lignes suivantes :

```
registry={URL_REGISTRY}
_auth = {CLE_AUTH}
email = {ADRESSE_MAIL}
always-auth = true
debug=true
save-prefix='~'
cache-min=3600
phantomjs_cdnurl={URL_ARTIFACTORY}/repository-npm-tiers/phantomjs
disturl={URL_ARTIFACTORY}/repository-npm-tiers
```

Pour les valeurs des 3 variables non renseignées, il pourra être nécessaire de contacter le pôle architecture.


## Gérer plusieurs versions de node

Parfois, il peut s'avérer necessaire de switcher de versions de node. Pour réaliser cela, nous allons utiliser la librairie [n](https://www.npmjs.com/package/n).
Ci-dessous, la configuration pour l'installation et l'utilisation de `n`.

### Installation

Avant d'installer n, nous allons définir les variables d'environnement suivantes

- N_PREFIX : indique le chemin d'installation des versions de node. Par défaut `/usr/local/`
- PROJECT_NAME : nom du projet fournissant les versions de node. Par défaut `node`.
- PROJECT_URL: l'url de base utilisée pour télécharger les versions de node. Par défaut `https://nodejs.org/dist/`

Par défaut, les users n'ayant pas le droit d'écriture sur `usr/local`, nous allons valoriser `N_PREFIX` à `~/Dev/tools/node`.
On gardera la même valeur pour `PROJECT_NAME` à savoir `node`. Attention, on la définira quand-même, autrement, la valeur de `PROJECT_URL` ne sera pas prise en compte.
Pour cette dernière variable, on indiquera l'url de l'artifactory.

En resumé: 

```
export N_PREFIX=~/Dev/tools/node
export PROJECT_NAME="node"
export PROJECT_URL="url_registry"
```

ces trois lignes peuvent être ajoutées à la suite du fichier ~/.profile au lieu de les déclarer à chaque session.

Une fois, ces variables déclarées, il faudra installer `n` en global :

```
npm i -g n
```

Faire un `n --version` pour vérifier que l'installation s'est bien passée.

### Téléchargement et utilisation d'un version de node
Pour connaitre les versions de node disponibles:

 ```
 
 n project ls
 
 ```
 
 Pour installer la version 4.9.1 de node.
 
 ```
 
 n project 4.9.1
 
 ```
 
Cette commande changera la version de node utilisée sur la machine si la v 4.9.1 est déjà installée.
 
`n` place la version activée de node dans `~/Dev/tools/node\n\bin` il faudra créer un lien symbolique depuis `~/bin` pour `node`, `npm` et `node_module` (qui se trouve dans`~/Dev/node/n/lib/`).
On supprimera ces liens s'ils existent déjà.
Se positionner dans le repertoire `~/bin` et exécuter les commandes suivantes

```
rm node # si le lien existe déjà
ln -s ~/Dev/tools/node/n/bin/node
rm npm # si le lien npm existe déjà
ln -s ~/Dev/tools/node/n/lib/node_modules/npm/bin/npm
rm node_modules # si le lien existe déjà
ln -s ~/Dev/tools/node/n/lib/node_modules
```
