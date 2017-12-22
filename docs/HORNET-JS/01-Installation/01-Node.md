# Node  #

Ce document a pour but de décrire les opérations à effectuer pour installer et configurer un environnement de développement pour `hornet-js` version `5.1.X` et supérieur.

## Composition de l’environnement ##

L’environnement d’exécution Hornet est composé des éléments suivants :

- `NodeJS 6.10 LTS`

L’environnement de développement intégré (IDE) retenu est `Webstorm 2017.2.5`. Il est aussi possible d'utiliser Visual Studio Code.

## Configuration de l'environnement d'exécution ##

La version de NodeJS supportée par Hornet est la `6.10 LTS`

### Installation de NodeJS ###

- Récupérer le binaire correspondant sur le site de nodejs

[https://nodejs.org/en/download/releases/](https://nodejs.org/en/download/releases/)

- dézipper le sources sous ~/Dev/tools/node/node-6.10LTS

### Création de lien symbolique ###

Dans un terminal, taper les commandes suivantes:

```
cd  ~/bin

ln -s ~/Dev/node-v6.10.3-linux-x64/bin/node
ln -s ~/Dev/node-v6.10.3-linux-x64/bin/npm

```

### Vérifier NodeJS ###

- Ouvrir une console
- Taper `node --version`
- Doit s'afficher en retour : `v6.10.x`

### Vérifier NPM ###

- Ouvrir une console
- Taper `npm --version`
- Doit s'afficher en retour : `3.10.10`

#### Configuration NPM

Dans votre répertoire bin, modifier ou créer le fichier .npmrc :

```
registry={URL_REGISTRY}
_auth = {CLE_AUTH}
email = {ADRESSE_MAIL}
always-auth = true
debug=true
save-prefix='~'
```

Pour les valeurs des 3 variables non renseignées, il pourra être nécessaire de contacter le pôle architecture.