# Le composant `server`

Ce composant sert à lancer le serveur `http`. Il est donc responsable de l'initialisation du serveur `Nodejs` au travers de `Express`.

## Interface du composant

Ce composant doit être instancié avec un objet de type ServerConfiguration et en facultatif un object de type HornetMiddlewareList qui encapsule le tableau de middlewares à utiliser.

Ce composant expose une classe `Server`:

### Attributs

| Nom | Visibilité | Type | Description |
| --------- | ----------- | -------- | ----------- |
| app | private | Express| Application Express |
| server | private |  | serveur (http/https) |
| monitor | private | Monitor |  |
| keepAlive | private | boolean |  |
| keepAliveTimeOut | private | number |  |
| port | private | number | Port sur lequel le serveur est monté |
| maxConnections | private | number | Nombre de connexions max au serveur |
| timeOut | private | number | Délai d'attente |
| protocol | private | string | http/https |

### Méthodes

| Nom | type | Description |
| --------- | ----------- | ----------- |
| start | public | Méthode permettant de démarrer le serveur selon les paramètres |
| createServer | private | Selon le protocole spécifié (http/https), création du serveur |
| init | private | Initialisation du serveur NodeJs: chargement d'Express - Application des middlewares |

### Description

Le constructeur initialise complètement le serveur `http`/`https` avec la liste de `middlewares` passés en paramètre :

- Chargement de `express`
- Configuration de `bodyParser` dans `express` pour profiter de la conversion automatique JSON & url encoded body
- Configuration d'un namespace (à partir du module `continuation-local-storage`) afin de générer une clé unique à chaque requête
- Configuration du chemin vers le contenu statique
- Configuration du module `express-state` afin de pouvoir envoyer facilement des informations côté client par sérialisation/désérialisation JSON
- Configuration du layout afin de pouvoir utiliser le moteur de template pour le rendu des composants React dans une page côté serveur
- Configuration du routeur `director` afin d'initialiser toutes les routes applicatives
- Configuration d'un middleware `express` afin de récupérer toutes les requêtes qui ne seraient pas gérées par `director`
- Configuration du protocole de transport : `http/https`

Le constructeur a la signature suivante :

```javascript

Server(appConfig: ServerConfiguration, hornetMiddlewareList: HornetMiddlewares.HornetMiddlewareList);

```

```javascript
var configServer:ServerConfiguration = {
    appComponent: HornetAppReact,
    errorComponent: HornetErrorComponent,
    defaultRoutesClass: new Routes(),
    dispatcher: appDispatcher,
    httpsOptions = {
        key: fs.readFileSync("server.https.key", "utf8"),
        cert: fs.readFileSync("server.https.cert", "utf8"),
        passphrase: utils.config.get("server.https.passphrase")
    }    
    internationalization: new AppI18nLoader(),    
    layoutComponent: HornetLayoutReact,
    loginUrl: utils.config.get("authentication.loginUrl"),
    logoutUrl: utils.config.get("authentication.logoutUrl"),
    menuConfig: Menu.menu,
    publicZones: [
        utils.config.get("welcomePage")
    ],    
    routesLoaderfn: routeLoader,
    serverDir: __dirname,
    sessionStore: null,
    staticPath: "../static",
    welcomePageUrl: utils.config.get("welcomePage")
};
```

| Paramètre | Obligatoire | Description |
| --------- | ----------- | ----------- |
| serverDir | X | Répertoire courant du script, utilisé pour reconstruire les liens relatifs |
| staticPath | X | Répertoire relatif (par rapport à la variable 'serverDir') vers les ressources statiques |
| appComponent | X | Composant React principal à utiliser pour rendre les pages. Ce composant est instancié par le routeur côté serveur et par le composant 'client' côté client. Côté serveur, la page courante à rendre lui est fourni par le routeur grâce à la propriété 'composantPage'. [Voir le composant 'client' pour le fonctionnement côté client](/hornetshowroom/composant/page/hornet-js/conception/javascript/configuration/client) |
| layoutComponent | X | Composant React chapeau à utiliser pour rendre les pages. Ce composant est instancié par le routeur côté serveur la premiere fois, il contient le cadre html, header, body et le composant appComponent.  |
| errorComponent | X | Composant React invoqué en lieu et place de la page courante à rendre en cas d'erreur non gérée par le routeur. Ce composant doit être minimaliste et doit aller chercher dans le 'NotiticationStore' les erreurs qui se sont produites |
| defaultRoutesClass | X | Classe permettant d'instancier les routes. [Voir le composant 'routeur' pour plus de détails](/hornetshowroom/composant/page/hornet-js/tutoriel/javascript/gestion-des-routes) |
| routesLoaderfn | X | Fonction de chargement des routes en mode lazy par le routeur. Elle prend en argument le nom du fichier contenant les routes et retourne la classe chargée. Coté serveur cette fonction effectue juste un 'require' du fichier passé en paramètre. [Voir le composant 'client' pour le fonctionnement côté client](/hornetshowroom/composant/page/hornet-js/conception/javascript/configuration/client)|
| internationalization| X | Internationalization [Voir documentation](/hornetshowroom/composant/page/hornet-js/tutoriel/javascript/internationalisation)|
| loginUrl | X | L'url relative d'authentification. "/login" dans la majorité des cas, que l'application soit CASsifiée ou non |
| logoutUrl | X | L'url relative de déconnexion. "/logout" dans la majorité des cas, que l'application soit CASsifiée ou non |
| welcomePageUrl | X | L'url de la page d'accueil. Si la page d'accueil doit être accessible sans authentification, il faut définir une url dédiée (ex: "/accueil") au lieu de "/" et définir l'url "/accueil" dans les "publicZones". Sinon laisser "/" |
| menuConfig | | Navigation menu [Voir le composant 'navigation'](/hornetshowroom/composant/page/hornet-js/composants/layouts/navigation) |
| publicZones | | Liste de chemins accessibles sans authentification. Toute requête dont l'url commence par un des chemin sera accessible librement. Exemple : "/public/" => les requêtes CONTEXT_PATH/public/maPage1 et CONTEXT_PATH/public/abc/maPage2 seront accessibles librement |
| httpsOptions | | Configuration du serveur pour répondre sur du https. Les options sont celles requises par le serveur https de nodejs. Si cette clé n'est pas alimenté, le serveur démarre en mode http. |

## Configuration des middlewares

Les middlewares par défaut du framework (l'ordre est important !) :

```javascript
import HornetMiddlewares = require("hornet-js-core/src/middleware/middlewares")

const hornetMiddlewareList = new HornetMiddlewares.HornetMiddlewareList();
```

Cette liste contient par défaut les middlewares suivants :

```javascript
export const DEFAULT_HORNET_MIDDLEWARES: Array<Class<AbstractHornetMiddleware>> = [
  HornetContextInitializerMiddleware,

  LoggerTIDMiddleware,
  SecurityMiddleware,
  WelcomePageRedirectMiddleware,
  WithoutSlashPageRedirectMiddleware,
  StaticNodeHttpHeaderMiddleware,
  StaticPathMiddleware,
  StaticPathErrorMiddleware,
  BodyParserJsonMiddleware,
  BodyParserUrlEncodedMiddleware,
  MockManagerMiddleware,
  SessionMiddleware,
  InternationalizationMiddleware,
  ChangeI18nLocaleMiddleware,
  LoggerUserMiddleware,
  CsrfMiddleware,
  MulterMiddleware,
  MenuConfigMiddleware,
  RouterServerMiddleware,
  UserAccessSecurityMiddleware,
  DataRenderingMiddleware,
  UnmanagedDataErrorMiddleware
];
```

Il est possible d'ajouter / supprimer un middleware en plus de ceux par défaut:

```javascript


      let hornetMiddlewareList = new HornetMiddlewares.HornetMiddlewareList();

      // ajout du middleware MyMiddleware1 AVANT le middleware Csrf :
      hornetMiddlewareList.addBefore(MyMiddleware1, HornetMiddlewares.CsrfMiddleware)

      // ajout du middleware MyMiddleware2 APRES le middleware Session :
      hornetMiddlewareList.addAfter(MyMiddleware2, HornetMiddlewares.SessionMiddleware)

      // suppression du middleware MockManager :
      hornetMiddlewareList.remove(HornetMiddlewares.MockManagerMiddleware);


```

## Configuration en mode https

L'ajout des options `https` dans la configuration serveur active par défaut le mode. Sinon c'est le protocole `http` qui est utilisé.

Ensemble des options pouvant être fournies au serveur pour la mise en place du protocole `https` :  

```javascript
declare module "https" {
export interface ServerOptions {
        pfx?: any;
        key?: any;
        passphrase?: string;
        cert?: any;
        ca?: any;
        crl?: any;
        ciphers?: string;
        honorCipherOrder?: boolean;
        requestCert?: boolean;
        rejectUnauthorized?: boolean;
        NPNProtocols?: any;
        SNICallback?: (servername: string) => any;
    }
//    ...


```

Ex : génération d'un certificat auto-signé et d'une clé pour tester le mode https

```shell
openssl req -x509 -sha256 -newkey rsa:2048 -keyout key.pem -out cert.pem -days XXX
``

```javascript
  "server": {
    ...
    "https": {
      "cert": "/path/to/client/certificate/in/pem/format",
      "key": "/path/to/client/private/key/in/pem/format",
      "passphrase": "password"
    }
```

Note :

Lors du premier accès à l'application, le navigateur bloque l'affichage du fait que le certificat est auto-signé. Dans ce cas, approuver le certificat.

## Paramétrage/déclaration de certificat/clés privées (KeyStore) pour les appels d'api

Il est possible de déclarer des certificats / clés privées nécessaires aux appels de service ou d'api.
Cette configuration se fait par la présence d'une clé de configuration "keystore" dans la configuration, cet objet permet de paramétrer :

- des autorités de certification pour la validation d'accès SSL en présence de certificats auto-signés. La clé "CAs" est un tableau contenant une liste de chemins vers des certificats d'autorités de certification au format PEM.
- des certificats / clés privées pour permettre une authentification SSL. Pour cela la clé "CERTs" est un tableau contenant une liste de chemins vers les certificats (clés publiques) au format PEM, et la clé "KEYs" est un tableau d'objet contenant le chemin et la passphrase de chaque clé privée.

Il est également possible de déclarer un seul conteneur de type PKCS12 contenant directement la liste de tous les certificats et de toutes les clés privées, il sera décrypté avec sa passphrase.

Exemple de configuration du keystore :

```json
  ...
  "keystore": {
    // Liste des autorités de certification
    "CAs": [
      "/path/to/igca.pem"
    ],

    // Cas d'authentification SSL
    "CERTs" : [
      "/path/to/client/certificate/in/pem/format"
    ],
    "KEYs": [
      {
        "file": "/path/to/client/private/key/in/pem/format",
        "passphrase": "abcdefgh"
      }
    ],


    // Cas d'un conteneur PKCS12
    "PKCS12": {
      "file": "/path/to/pkcs12",
      "passphrase": "abcdefgh"
    }
  },
  ...
```

Ces éléments de configuration sont automatiquement appliqués à nodejs lors de l'instanciation de la classe Server de hornet-js-core.

## Cas d'utilisation dans une application Hornet

Chaque application `Hornet` dispose d'un fichier server.ts (hormis les applications en FullSPA) lequel va instancier le `Server` de `hornet-js-core` en lui fournissant un objet de configuration, ainsi qu'une liste de middleware.

```javascript

export class Server {

    static configure() {

        let configServer: ServerConfiguration = {
            serverDir: __dirname,
            staticPath: "../static",
            appComponent: HornetApp,
            layoutComponent: HornetLayout,
            errorComponent: ErrorPage,
            defaultRoutesClass: new Routes(),
            routesLoaderPaths: ["src/routes/"],
            internationalization: new AppI18nLoader(),
            menuConfig: Menu.menu,
            loginUrl: Utils.config.get("authentication.loginUrl"),
            logoutUrl: Utils.config.get("authentication.logoutUrl"),
            welcomePageUrl: Utils.config.get("welcomePage"),
            publicZones: [
                Utils.config.get("welcomePage")
            ]
        };

        const key = Utils.config.getOrDefault("server.https.key", false);
        const cert = Utils.config.getOrDefault("server.https.cert", false);
        if (key && cert) {
            configServer.httpsOptions = {
                key: fs.readFileSync(key, "utf8"),
                cert: fs.readFileSync(cert, "utf8"),
                passphrase: Utils.config.get("server.https.passphrase")
            };
        }
        return configServer;
    }

    static middleware() {

        let hornetMiddlewareList = new HornetMiddlewares.HornetMiddlewareList()
            .addAfter(PageRenderingMiddleware, HornetMiddlewares.UserAccessSecurityMiddleware)
            .addAfter(UnmanagedViewErrorMiddleware, HornetMiddlewares.DataRenderingMiddleware);
        hornetMiddlewareList.addAfter(AuthenticationAPIMiddleware, HornetMiddlewares.ChangeI18nLocaleMiddleware);
        return hornetMiddlewareList;
    }

    static startApplication() {
        let server = new HornetServer.Server(Server.configure(), Server.middleware());
        server.start();
    }

}

```


le `Server` applicatif est ensuite instancié dans le fichier `index.ts` de l'application

```javascript

// lancement de l'application
import { Server } from "src/server";
Server.startApplication();

```