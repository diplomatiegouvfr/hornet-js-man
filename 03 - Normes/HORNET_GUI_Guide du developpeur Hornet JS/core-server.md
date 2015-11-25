# Le composant 'server' du module hornet-js-core

## Présentation

Ce composant sert à lancer le serveur `http` proprement dit. Il est donc responsable de l'initialisation proprement dite du serveur NodeJS.

## Interface du composant

Ce composant doit être instancié avec un objet de type ServerConfiguration et en facultatif un tableau de middlewares.

Ce composant expose deux fonctions:

- start : démarre le serveur http

### constructor

Le constructeur initialise complètement le serveur `http` avec la `middlewares` passés en paramètre :

- Chargement de 'express'
- Configuration de 'bodyParser' dans 'express' pour profiter de la conversion automatique JSON & url encoded body
- Configuration d'un namespace (à partir du module 'continuation-local-storage') afin de générer une clé unique à chaque reqûete
- Configuration du chemin vers le contenu statique
- Configuration du module 'express-state' afin de pouvoir envoyer facilement des informations côté client par sérialisation/désérialisation JSON
- Configuration du layout afin de pouvoir utiliser le moteur de template pour le rendu des composants React dans une page côté serveur
- Configuration du routeur 'director' afin d'initialiser toutes les routes applicatives
- Configuration d'un middleware 'express' afin de récupérer toutes les requêtes qui ne seraient pas gérées par 'director'

#### arguments du constructeur

Le constructeur a la signature suivante : 
```javascript
Server(appConfig:ServerConfiguration, middlewares:Array<typeof HornetMiddlewares.AbstractHornetMiddleware>)
```

```javascript
var configServer:ServerConfiguration = {
    serverDir: __dirname,
    staticPath: "../static",
    appComponent: HornetAppReact,
    layoutComponent: HornetLayoutReact,
    errorComponent: HornetErrorComponent,
    defaultRoutesClass: new Routes(),
    sessionStore: null,
    routesLoaderfn: routeLoader,
    internationalization: new AppI18nLoader(),
    dispatcher: appDispatcher,
    menuConfig: Menu.menu,
    loginUrl: utils.config.get("authentication.loginUrl"),
    logoutUrl: utils.config.get("authentication.logoutUrl"),
    welcomePageUrl: utils.config.get("welcomePage"),
    publicZones: [
        utils.config.get("welcomePage")
    ]
};
```

| Paramètre | Obligatoire | Description |
| --------- | ----------- | ----------- |
| serverDir | X | Répertoire courant du script, utilisé pour reconstruire les liens relatifs |
| staticPath | X | Répertoire relatif (par rapport à la variable 'serverDir') vers les ressources statiques |
| appComponent | X | Composant React principal à utiliser pour rendre les pages. Ce composant est instancié par le routeur côté serveur et par le composant 'client' côté client. Côté serveur, la page courante à rendre lui est fourni par le routeur grâce à la propriété 'composantPage'. [Voir le composant 'client' pour le fonctionnement côté client](core-client.md) |
| layoutComponent | X | Composant React chapeau à utiliser pour rendre les pages. Ce composant est instancié par le routeur côté serveur la premiere fois, il contient le cadre html, header, body et le composant appComponent.  |
| errorComponent | X | Composant React invoqué en lieu et place de la page courante à rendre en cas d'erreur non gérée par le routeur. Ce composant doit être minimaliste et doit aller chercher dans le 'NotiticationStore' les erreurs qui se sont produites |
| defaultRoutesClass | X | Classe permettant d'instancier les routes. [Voir le composant 'routeur' pour plus de détails](core-routeur.md) |
| routesLoaderfn | X | Fonction de chargement des routes en mode lazy par le routeur. Elle prend en argument le nom du fichier contenant les routes et retourne la classe chargée. Coté serveur cette fonction effectue juste un 'require' du fichier passé en paramètre. [Voir le composant 'client' pour le fonctionnement côté client](core-client.md)|
| internationalization| X | Internationalization [Voir documentation](Internationalisation.md)|
| dispatcher | X | Composant Fluxible qui se charge du dispatcher. Ce composant servira à créer le contexte fluxible pro### start

Cette fonction démarre le serveur applicatif, elle ne prend aucun argument.pre à chaque requête. [Voir le composant 'client' pour le fonctionnement côté client](core-client.md) |
| sessionStore | X | Store spécifique pour stocker les sessions. Laisser à `null`.
| loginUrl | X | L'url relative d'authentification. "/login" dans la majorité des cas, que l'application soit CASsifiée ou non |
| logoutUrl | X | L'url relative de déconnexion. "/logout" dans la majorité des cas, que l'application soit CASsifiée ou non |
| welcomePageUrl | X | L'url de la page d'accueil. Si la page d'accueil doit être accessible sans authentification, il faut définir une url dédiée (ex: "/accueil") au lieu de "/" et définir l'url "/accueil" dans les "publicZones". Sinon laisser "/" |
| menuConfig | | navigation menu [Voir le composant 'navigation'](composant-navigation.md) |
| publicZones | | Liste de chemins accessibles sans authentification. Toute requête dont l'url commence par un des chemin sera accessible librement. Exemple : "/public/" => les requêtes CONTEXT_PATH/public/maPage1 et CONTEXT_PATH/public/abc/maPage2 seront accessibles librement |

Les middlewares par défaut du framework (l'ordre est important !) :

```javascript
import HornetMiddlewares = require("hornet-js-core/src/middleware/middlewares")

var middlewares = [
    HornetMiddlewares.LoggerTIDMiddleware,
    HornetMiddlewares.DisableKeepAliveMiddleware,
    HornetMiddlewares.SecurityMiddleware,
    HornetMiddlewares.WelcomePageRedirectMiddleware,
    HornetMiddlewares.StaticPathMiddleware,
    HornetMiddlewares.BodyParserJsonMiddleware,
    HornetMiddlewares.BodyParserUrlEncodedMiddleware,
    HornetMiddlewares.MockManagerMiddleware,
    HornetMiddlewares.SessionMiddleware,
    HornetMiddlewares.LoggerUserMiddleware,
    HornetMiddlewares.CsrfMiddleware,
    HornetMiddlewares.MulterMiddleware,
    HornetMiddlewares.RouterDataMiddleware,
    HornetMiddlewares.RouterViewMiddleware,
    HornetMiddlewares.ErrorMiddleware
];
```

## Instanciation et démarrage du serveur

```javascript
// création du serveur
var server = new Server(configServer, middlewares);

// démarrage du serveur
server.start();
```
