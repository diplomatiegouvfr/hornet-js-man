# Le composant 'routeur'

## Présentation

Ce composant est central dans l'application car il gère toutes les actions à exécuter lors d'un changement de route (= changement d'url).

Le routeur de Hornet-js est basé sur le composant isomorphe `director` (https://github.com/flatiron/director) qu'il vient surcharger afin de proposer un cadre de développement unifié

## Principe de fonctionnement

Lors du chargement de l'application, le routeur est instancié avec des routes de bases ainsi que des routes à chargement différé (routes en mode lazy).

A chacune de ces routes correspond une fonction qui est exécutée automatiquement par le routeur lorsque celle-ci est activée par `director`.

Cette fonction doit retourner un objet ([voir ci-dessous](#instanciation-des-routes)) qui va permettre au routeur de résoudre les actions à effectuer sur cette route et donc de rendre correctement la page.

## Instanciation des routes en mode non lazy

Pour configurer les routes, chaque application doit fournir une classe étendant la classe `AbstractRoutes` .

Cette classe dispose d'une méthode `addPageRoute` qui permet d'ajouter une route liée à une page :

```javascript
import { AbstractRoutes, PageRouteInfos, PUBLIC_ROUTE } from "hornet-js-core/src/routes/abstract-routes";

import { FichePartenairePage, PAR_MODE_CREER } from "applitutoriel-js-common/src/views/par/par-fpa-page";
import { HomePage } from "applitutoriel-js-common/src/views/gen/gen-hom-page";

export class Routes extends AbstractRoutes {
    constructor() {
        super();

        /*Ajout d'une route accueil*/
        this.addPageRoute("/accueil",
           () => new PageRouteInfos(HomePage),
           PUBLIC_ROUTE
        );

        /*Ajout d'une route création partenaire*/
        this.addPageRoute(URL_PAR_CREER,
            () => new PageRouteInfos(FichePartenairePage, {mode: PAR_MODE_CREER}, FichePartenaireServiceImpl),
            Roles.ADMIN
        );
    }
}
```

Cette fonction prend les arguments suivants:

| Paramètre | Description |
|------- | ----------- |
| path | L'url de la route. Nous préconisons une regex. |
| handler | La méthode qui est appelée par le routeur lorsque la route est activée |
| [authorisation](##authorisations) | Les rôles qui ont accès à la route |

Lorsqu'une route est activée, le routeur appelle automatiquement la fonction '*handler*' avec les arguments suivants:

| Paramètre | Description |
|------- | ----------- |
| param1 | Optionnel. Le premier paramètre sur une route de type: /url//(\\w+) |
| param2 | Optionnel. Le second paramètre sur une route de type: /url//(\\w+)//(\\w+) |
| paramX | Optionnel. Le Xeme paramètre sur une route de type: /url//(\\w+)/...//(\\w+) |

Cette fonction doit préparer les actions que le routeur exécutera ensuite afin de rendre correctement la page demandée.
Le retour de la fonction est de type `PageRouteInfos`. les paramètres du constructeur sont les suivants:

| Paramètre | Description |
|------- | ----------- |
| viewComponent | Le composant React à rendre sur cette page qui implémente IHornetPage |
| attributes | Optionnel. Les attributs passés a la page |
| service | Optionnel. Le service qui sera lié a la page |

Cette classe dispose également d'une méthode `addDataRoute` qui permet d'ajouter une route de data :

```javascript
import { AbstractRoutes, PageRouteInfos, PUBLIC_ROUTE } from "hornet-js-core/src/routes/abstract-routes";

import { FichePartenairePage, PAR_MODE_CREER } from "applitutoriel-js-common/src/views/par/par-fpa-page";
import { HomePage } from "applitutoriel-js-common/src/views/gen/gen-hom-page";

export class Routes extends AbstractRoutes {
    constructor() {
        super();

        /*Ajout d'une route accueil*/
        this.addPageRoute("/accueil",
           () => new PageRouteInfos(HomePage),
           PUBLIC_ROUTE
        );

        /*Ajout d'une route data*/
        this.addDataRoute("",
            () => new DataRouteInfos(FichePartenairesAction.EcrirePartenaire, {}, PartenaireServiceImpl),
            "post"
        );

    }
}
```

Cette fonction prend les arguments suivants:

| Paramètre | Description |
|------- | ----------- |
| path | L'url de la route. Nous préconisons une regex. |
| handler | La méthode qui est appelée par le routeur lorsque la route est activée |
| method | La méthode http sur laquelle la route doit s'activer: Valeurs possibles: **get**, **post**, **put**, **delete**, ... |
| authorization | Les rôles qui ont accès a la route |

Le retour de la fonction '*handler*' est de type `DataRouteInfos`. Les paramètres du constructeur sont les suivants:

| Paramètre | Description |
|------- | ----------- |
| action |Action à appelée |
| attributes | Optionnel. Les attributs passés à l'action |
| service | Optionnel. Le service qui sera lié a l'action |


### Le contexte de la requete
Cet objet permet d'accéder aux informations courantes de la requête:

| Attribut | Description |
|------- | ----------- |
| req | Les éléments de la requête|
| res | La réponse|

L'objet *req* contient notamment les propriétés suivantes:

| Attribut | Description |
|------- | ----------- |
| body | Le corps de la requête sous la forme d'un objet javascript |
| query | Les paramètres de la requête sous la forme d'un objet javascript.<br /> Exemple: page/XX/YY => {param1:XX, param2:YY} |
|session| La session courante de l'utilisateur|

### Notes sur les actions et les promises

Les actions sont chainées grâces à des promises.
Ceci a les implications suivantes:

- Si une action échoue, les promises suivantes ne sont pas exécutées
- Chaque action reçoit en paramètre le résultat de la promise précédente.

Exemple:

```javascript
import {RouteActionService} from "hornet-js-core/src/routes/abstract-routes";
import {RouteAction} from "hornet-js-core/src/routes/abstract-routes";
...

export class SupprimerPage extends RouteActionService<{id: string}, RecherchePartenaireService> {
    execute(): Promise<any> {
        return this.getService().supprimerPage(this.attributes.id)
    }
}
```

Si l'action ne nécessite pas de service, l'action doit étendre RouteAction, sinon, elle doit étendre RouteActionService.

`this.getService()` permet de faire appel au service lié à l'action qui a été définit dans la route appelant l'action.
Ici nous 


## Instanciation des routes en mode lazy

La classe `AbstractRoutes` propose aussi une méthode `addLazyRoutes` qui prend les arguments suivants:

| Paramètre | Description |
|------- | ----------- |
| path | L'url parente du groupe de routes. Toutes les routes à charger auront comme base ce path. <br />Note: En interne le routeur génère une route qui match: `path\.*` |
| fileToLoad | Path (sous la forme d'un lien textuel) vers la classe de routes à charger. <br /> Voir la documentation de la fonction *routesLoaderfn* pour plus d'informations: [core-serveur](/hornetshowroom/composant/page/hornet-js/guide/guide_du_developpeur_js/server) et [core-client](/hornetshowroom/composant/page/hornet-js/conception/javascript/configuration/client)|

```javascript
import { AbstractRoutes, PageRouteInfos, PUBLIC_ROUTE } from "hornet-js-core/src/routes/abstract-routes";

export class Routes extends AbstractRoutes {
    constructor() {
        super();

        /*exemple de lazy routes*/
        this.addLazyRoutes("/produits", "pro/pro-routes");
    }
}
```

### Mode lazy côté serveur

Le mode `lazy` n'ayant pas d'intéret pour le serveur, celui-ci va charger toutes les routes en mémoire dès le chargement du routeur.

### Mode lazy côté client

Dès qu'une route `lazy` est détectée côté client, les actions suivantes sont effectuées par le routeur:

- Chargement de la classe de routes correspondante
- Suppression de la route créée automatiquement au chargement et qui s'applique sur tous les paths fils (`path\.*`).
- Insertion des nouvelles routes dans le routeur en remplacement de la route supprimée juste avant
- Relance d'un cycle de résolution de route.

## Différence entre DataRoute et PageRoute

Une route de type `Page` est définie à partir d'une page et d'un service alors que les routes de type `page` sont définies à partir d'un service et d'une action.

## Autorisations

Par défaut, une route est déclarée privée, il faudra être loggée avec n'importe quel profil.
Pour qu'une route soit publique, il faut ajouter en guise de paramètre `authorization` la valeur `PUBLIC_ROUTE`.
Si on veut spécifier un rôle particulier, il faudra déclarer un tableau du/des rôles pour lesquels l'accès à la route est autorisée.


## Ajout de filtres sur des patterns d'url

Le routeur offre la possibilité à l'application de definir des filtres sur des pattern d'url.

Exemple:

```javascript
import { AbstractRoutes, PageRouteInfos, PUBLIC_ROUTE } from "hornet-js-core/src/routes/abstract-routes";
...

export class Routes extends AbstractRoutes {
    constructor() {
        super();

        this.addPageRoute("/(\\w+)/(\\d+)",
            (mode, id) => new PageRouteInfos(FichePartenairePage, {mode: mode, id: id}, FichePartenaireServiceImpl),
            Roles.ADMIN
        );
    }
}
```

## Notes importantes côté client

Voici des points importants qui s'appliquent côté client:

- Le routeur est configuré en mode **html5 history**
- Un *eventListener* est automatiquement enregistré sur :

   - tous les évenements de type '**click**'
   - et portant sur un élement html de type '**a**'
   - dont la propriété '**href**' est valorisée et ne commence pas par '**#**'
   - et dont la propriété '**data-pass-thru**' n'est pas valorisée

Pour ces évènements l'action par défaut est annulée et le contenu de la propriété '**href**' est passé au routeur pour activer la nouvelle route.

Cet évènement permet ainsi d'avoir un fonctionnement SPA sans avoir à gérer explicitement l'évènement du click sur un lien.

   - le routeur est accessible dans l'objet **window** (*ce comportement est susceptible d'être modifié dans les versions supérieures de hornet 5.1.X*)

```javascript

window.routeur;

```

Dans une page de type `HornetPage`, il est possible de changer de route en appelant la fonction `navigateTo`:

```javascript

this.navigateTo(URL, {},() =>{
    callback();
});

```

Pour changer de route en passant des datas à la prochaine page il est possible d'appeler l'évenement méthode suivant:

```javascript

    fireHornetEvent(CHANGE_URL_WITH_DATA_EVENT.withData({url: url, data:data, cb : cb}));
    
```

## Basculement en mode rendu serveur

Côté client, le routeur est activé par défaut : une application Hornet fonctionne donc en mode SPA (cf Dossier d'architecture). Néanmoins, le framework offre la possibilité de basculer en mode `rendu serveur`. Dans ce mode, le routeur client est désactivé, et c'est le navigateur qui traite les routes (liens du menu permettant de changer d'écran, actions sur les tableaux, etc...), tout à fait comme une application web classique MPA. Visuellement, on constate qu'un rendu serveur complet est effectué à chaque action.

L'activation du mode `rendu serveur` est configurable au niveau du localStorage du navigateur. Une fonction javascript est fournie côté client afin de réaliser automatiquement cette configuration : setHornetJsGenerationServer. Les valeurs acceptées sont true et false.

Le code suivant, ajouté au niveau du client.ts (après l'initialisation du routeur), permet d'active le mode MPA :

```javascript
var readyCallback = function () {
    window.setHornetJsGenerationServer(true);
};
Client.initAndStart(configClient, readyCallback);
```

La valeur peut aussi être modifiée manuellement dans le navigateur lorsqu'il fournit des outils de développement le permettant (par exemple, sous Chrome, dans les DevTools, onglet Resources, partie Local Storage).

Remarque : évidemment, cette option n'a d'interêt qu'avec un serveur node : ne pas l'activer en mode fullSpa.

## Niveau de Log

Côté client, il est possible de définir le niveau de log au niveau du localStorage du navigateur. Une fonction javascript est fournie côté client afin de réaliser automatiquement cette foncfiguration: setHornetJsLogLevel.
Les valeurs acceptées sont les suivantes:

- `ALL`
- `TRACE`
- `DEBUG`
- `INFO`
- `WARN`
- `ERROR`
- `FATAL`
- `OFF`
