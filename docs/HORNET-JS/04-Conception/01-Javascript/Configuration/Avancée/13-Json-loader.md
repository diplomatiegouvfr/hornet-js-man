# Le composant 'json-loader'

Le composant json-loader est fourni par hornet-js-utils, et met à disposition une classe (JSONLoader) dotée de deux méthodes statiques :

* allowJSON5()
* load(filePath:string, encoding?:string)

La méthode `load` attend deux paramètres en entrée : le chemin du fichier, et l'encodage (facultatif). Elle va lire et parser le fichier JSON fourni en entrée, et elle renvoie l'objet javascript correspondant.

La méthode `allowJSON5` permet d'autoriser l'utilisation du format JSON5 dans les fichiers json, qu'il soit chargé :

- avec la méthode `JSONLoader.load`
- en faisant `require("monFichierJson")`
- ou en faisant `JSON.parse(MonContenuJSON)`

JSON5 est une adaptation moins stricte du format JSON permettant :

- l'utilisation de commentaires (//, /* */)
- de laisser des virgules en trop en fin de tableau ou d'objet
- d'avoir des valeurs multilignes
- ...

Pour plus d'informations, consulter le site [json 5](http://json5.org/)
 
## Chargement du fichier log4js.json

Le fichier de configuration json pour log4js (cf chapitre sur le composant server-log-configurator) est chargé par le module json-loader. Le chemin complet est préalablement constitué de manière dynamique à partir de variables d'environnement (indiquées ci-dessous en majuscules) :

* Vérification du mode production : mode actif si `NODE_ENV`=production
* Si mode production actif :
	* chemin du répertoire de configuration : valeur de `HORNET_CONFIG_DIR_INFRA`.
	* nom du fichier : log4js.json. Si `NODE_APP_INSTANCE` est défini, le nom du fichier est postfixé avec un tiret, suivi de la valeur de `NODE_APP_INSTANCE` (par exemple, `log4js-1.json`).
* Si mode développement :
	* chemin du répertoire de configuration : valeur de `HORNET_CONFIG_DIR_APPLI`
	* nom du fichier : `log4js.json`
