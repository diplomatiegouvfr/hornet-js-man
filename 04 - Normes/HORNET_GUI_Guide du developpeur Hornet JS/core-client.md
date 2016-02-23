# Le composant 'client' du module hornet-js-core

## Présentation

Ce composant est le point d'entrée du framework côté client. Il est donc responsable, entre autre, de l'initialisation du routeur côté client.

## Interface du composant

Ce composant expose deux fonctions:

### getLoggerBuilder

Cette fonction retourne la fonction d'initialisation des loggers de l'application côté client.

Voici ses arguments:

| Paramètre | Description |
|------- | ----------- |
| remoteLog | Pour activer ou non la remontée des messages de logs des clients vers le serveur. *Attention: cette fonction provoque l'émission de nombreuses requêtes POST vers le serveur NodeJS* |
| logLevel | Le niveau de log minimum (INFO par défaut) |
[LoggerBuilder - paramètres]

### initAndStart

Cette fonction initialise complètement le client avec le routage applicatif.

Elle effectue les opérations suivantes:

- Réhydratation des stores et de la configuration applicative
- Enregistrement d'une fonction de callback spéciale auprès du 'PageInformationsStore' afin de lancer le premier rendu React une fois la première route résolue
- Configuration du routeur 'director' afin d'initialiser toutes les routes applicatives
- Démarrage du routeur

#### Paramétrage de la fonction 'initAndStart'

La fonction *initAndStart* utilise un objet de type ClientConfiguration.  
	
Note: Cet objet à vocation à être simplifié suite à la publication de la version 5.0.X

```javascript
var configClient:clientNs.ClientConfiguration = {
    appComponent: hornetAppReact,
    errorComponent: hornetErrorReact,
    defaultRoutesClass: new routes(),
    routesLoaderfn: routeLoader,
    dispatcherLoaderFn: getDispatcher
};
```

| Paramètre | Description |
|------- | ----------- |
| appComponent | Composant React principal à utiliser pour rendre les pages. Ce composant est instancié dans la fonction 'initAndStart'. Côté client c'est auprès du 'PageInformationsStore' que ce composant doit aller chercher la page courante à rendre. [Voir le composant 'server' pour le fonctionnement côté serveur](core-server.md) |
| errorComponent | Composant React invoqué en lieu et place de la page courante à rendre en cas d'erreur non gérée par le routeur. Ce composant doit être minimaliste et doit aller chercher dans le 'NotiticationStore' les erreurs qui se sont produites |
| defaultRoutesClass | Classe permettant d'instancier les routes. [Voir le composant 'routeur' pour plus de détails](core-routeur.md)|
| routesLoaderfn | Fonction de chargement des routes en mode lazy par le routeur. Elle prend en argument le nom du fichier contenant les routes ainsi qu'une fonction de callback à appeler avec le composant en argument. Côté client elle permet de charger les routes de manière asynchrone (fonctionnement proposé par le module 'webpack'). Cette fonction est construite automatiquement lors du build du projet, **voir la partie dédiée ci-dessous**.  [Voir le composant 'server' pour le fonctionnement côté serveur](core-server.md)|
| dispatcherLoaderFn | Fonction chargée de retourner un contexte Fluxible. Pour la partie client c'est toujours la même instance qui est retournée par cette fonction. [Voir le composant 'server' pour le fonctionnement côté serveur](core-server.md) |
[ClientConfiguration - attributs] 

## La fonction de chargement des routes à la volée (routesLoaderfn)

### Problématique

Afin de permettre un chargement 'à la demande' des routes plutôt qu'un chargement complet de l'application au démarrage, il a fallu trouver un moyen de configurer le module 'webpack' pour ne pas embarquer toutes les routes (et leurs dépendances) lors de la compilation.

Pour autant webpack a besoin de connaitre l'exhaustivité des fichiers à packager au moment de la compilation.

### Solution proposée
La solution à cette problématique a été de developper, au sein du framework, un plugin webpack listant les fichiers contenant les routes de l'application et retournant la classe associée aux-dites routes dans une fonction asynchrone exécutée par le routeur lors du rendu des pages.

### Mise en oeuvre
Ce plugin est automatiquement exécuté par le module *hornetbuilder* lors de la compilation.

Les seules actions à effectuer par les applications sont les suivantes:

Dans le fichier client.js de l'application, avoir une méthode comportant le commentaire.

```javascript
// WEBPACK_AUTO_GENERATE_CLIENT_ROUTE_LOADING**
```

Exemple:
 
```javascript
function routeLoader(name:string, callback:any) {
    logger.info("routeLoader(" + name + ")");
    // WEBPACK_AUTO_GENERATE_CLIENT_ROUTE_LOADING
	return null;
}
```

Utiliser cette fonction dans la configuration du client:
 
```javascript
var configClient:clientNs.ClientConfiguration = {
  routesLoaderfn: routeLoader,
    ....
};
```

**Important :**    
 
Le plugin ne cherche que les fichiers javascript ayant le pattern suivant : **.\routes\\\*\*\Routes.ts**    
Par conséquent, il est **obligatoire** de nommer toutes les routes de l'application selon ce pattern

