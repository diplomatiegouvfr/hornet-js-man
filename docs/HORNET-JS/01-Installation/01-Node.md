# Node  #

Ce document a pour but de décrire les opérations à effectuer pour installer et configurer un environnement de développement pour `hornet-js` version `5.1.X` et supérieur.

## Composition de l’environnement ##

L’environnement d’exécution Hornet est composé des éléments suivants :

- `NodeJS 8.9 LTS`

L’environnement de développement intégré (IDE) retenu est `Webstorm 2017.3.4`. Il est aussi possible d'utiliser Visual Studio Code.

## Configuration de l'environnement d'exécution ##

La version de NodeJS supportée par Hornet est la `8.9 LTS`

### Installation de NodeJS ###

Récupérer le package DEB

Dans un terminal, taper la commande suivante:
```
sudo dpkg -i nodejs-8.9.4.deb
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
- Doit s'afficher en retour : `v8.9.x`

### Vérifier NPM ###

- Ouvrir une console
- Taper `npm --version`
- Doit s'afficher en retour : `5.6.0`

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
```

Pour les valeurs des 3 variables non renseignées, il pourra être nécessaire de contacter le pôle architecture.