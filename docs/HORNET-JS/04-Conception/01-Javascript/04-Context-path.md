# Context Path

## Principe

Cette notion doit permettre de déployer plus facilement plusieurs applications sur un même serveur en variabilisant le nom de l'application pour chaque instance de node lancée.

Cette foncitonnalité est assurée par l'utilisation d'une variable dans le fichier de configuration de l'application.

## Configuration

La variabilisation du *context path* dans l'application passe par la présence du paramètre suivant dans la conguration :

``` json
{
  "contextPath" : "applitutoriel"
}
```

## Utilisation

### Code Typescript

La fonction `buildContextPath(path)` est disponible dans hornet-js-utils, et permet l'ajout automatique du context path paramétré à l'url passée en paramètre.

Le context path est ajouté dans les cas suivants :

- Si la variable path commence par '/'
- Et si elle ne commence pas déjà par le context path

Ce second cas prévoit les appels multiples à la méthode dans l'ensemble de la chaine, ou dans l'imbrication de composants.

Le caractère final '/' est systématiquement supprimé des paths générés, les URLs ne devant pas faire référence à des dossiers.

``` javascript
import utils = require("hornet-js-utils");
utils.getContextPath()
```

### Composants React

La gestion du context dans les composants passe par l'utilisation par défaut d'un mixin injecté dans les composants Hornet.

Ce mixin propose la methode `genUrl(path)` qui va construire l'URL à appeler, via la méthode proposée par hornet-js-utils.

Ce mixin peut permettre d'ajouter des comportements génériques aux composants.

Dans les vues (React), il est possible d'utiliser le mixin `contextPathMixin` qui propose des méthodes de récupération de context applicatif `getContextPath`, `getContextPath`, `genUrlStatic`...

``` javascript
render() {
	var contextApp = this.getContextPath();
	var logoUrl = this.genUrlStatic(this.props.relativeLogoUrl),
}
```

## Fonctionnement framework

### Les routeurs

Les routeurs *data* et *view* chargent automatiquement le context path dans leur configuration de lancement afin de ne passer que par une seule et unique configuration.