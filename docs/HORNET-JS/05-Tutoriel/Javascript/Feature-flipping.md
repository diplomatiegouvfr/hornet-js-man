
# Principe

Il est parfois souhaité de rendre inacessible des pages ou des fonctionnalités d'une application, afin masquer des développements en cours de réalisation. Pour ce faire, il a été intégré le mécanisme de "feature flipping" permettant de bloquer l'accès aux pages concernées.
Ces dernières ne seront pas visibles dans le menu (point d'entrée pricipale). On interdit également l'accès à la page si jamais la route était connue.

# Mise en place de la solution

## Prérequis

Disposer des fichers suivants :
 - src/middleware/featuresflipping.ts (middleware)
 - src/actions/features-get-action.ts
 - src/resources/featureflipping.json (Fichier de configuration)
 - src/routes/common-server-routes.ts* ( routes associées aux mécanismes featureflipping)

Si ce n'est pas le cas, copier les fichiers déclarés en annexe.

 > Il est possible de renonmer les fichiers pour avoir un nom plus explicite, mais il ne faut pas oublier de faire ces changements dans les fichiers où les scripts seront importés.


## Implémentation

Importer dans le fichier `src/server.ts`, le middleware FeaturePageRenderingMiddleware et l'ajouter pour qu'il soit pris en compte.

```javascript
/* Au début du fichier */
 import { FeaturePageRenderingMiddleware } from "src/middleware/featuresflipping";

/* Ajouter la dernière ligne */
static middleware(): HornetMiddlewareList {
    let hornetMiddlewareList = new HornetMiddlewares.HornetMiddlewareList()
        .addAfter(PageRenderingMiddleware, HornetMiddlewares.UserAccessSecurityMiddleware)
        .addAfter(FeaturePageRenderingMiddleware, HornetMiddlewares.MenuConfigMiddleware);
```


### Création des routes  

- Ajouter les URLs à la fin du ficher dans `src/utils/urls `

```javascript
/** Features Flipping */
export const URL_STATUS = '/status';
export const URL_FEATURES = '/features';
```
### Création des actions associées

```javascript
//src/actions/cnt/status-get-actions.ts

import { RouteActionService } from 'hornet-js-core/src/routes/abstract-routes';
import { Utils } from 'hornet-js-utils';
import { Logger } from 'hornet-js-utils/src/logger';
import { CommonServiceData } from 'src/services/data/common-service-data';

const logger: Logger = Utils.getLogger('applitutoriel.actions.common.status-get');

export class StatusGet extends RouteActionService<any, CommonServiceData> {

    execute(): Promise<any> {
        return this.doExecute();
    }

    doExecute(): Promise<any> {
        return this.getService().getStatus()
            .then((response) => {
                logger.debug('RESPONSE-OK', response);
                try {
                    return response;
                } catch (error) {
                    logger.error('RESPONSE-KO', error);

                    return ([]);
                }
            });
    }
}
```

### Création des services associées

#### Interface service

```javascript
//src/services/data/common-service-data.ts

/**
 * Interface des services
 * @interface
 */
export interface CommonServiceData {
    getStatus(): Promise<any>;
    getFeatures(): Promise<any>;
}

```

#### Implémentation service

```javascript

// src/services/data/common-service-data-impl

import { HornetRequest } from 'hornet-js-core/src/services/hornet-superagent-request';
import { ServiceRequest } from 'hornet-js-core/src/services/service-request';
import { Utils } from 'hornet-js-utils';
import { Logger } from 'hornet-js-utils/src/logger';
import { Promise } from 'hornet-js-utils/src/promise-api';
import * as URLS from 'src/utils/urls';

import { CommonServiceData } from 'src/services/data/common-service-data';
import * as featureFlipping from "src/resources/featureflipping.json";
const logger: Logger = Utils.getLogger('data.common-service-data-impl');

export class CommonServiceDataImpl extends ServiceRequest implements CommonServiceData {

    getStatus(): Promise<any> {
        const request: HornetRequest = {
            url: this.buildUrl(URLS.URL_STATUS),
            method: 'get',
            noCached: true,
        };
        return this.fetch(request);
    }

    getFeatures(): Promise<any> {
        const request: HornetRequest = {
            url: this.buildUrl(URLS.URL_FEATURES),
            method: 'get',
            noCached: true,
        };

        //  return this.fetch(request);
        return Promise.resolve(featureFlipping);
    }
}

```


### Activation du feature flipping

Rappels des clés à modifier ou ajouter:

-  `navigation.json`

|nom de la clé| Description|type|
|-------------|------------|------|
|featureFlipping| Identifiant de la feature| String


- `featureflipping.json`

|nom de la clé| Description| Type |
|-------|-------------------------------------------------------------------|----------------------------------|
|id     | Identifiant de la feature                                         | String                           |
|label  | Libelle ou description minimaliste de la feature                  | String                           |
|enable | Etat de la feature : true est visible/ false ne sera pas visible  | Booleée                          |


### Configuration

Exemple de fichier de configuration, `featureflipping.json` :

```json
    {
    "featureFlipping": [{
        "id": "features_exemple2",
        "label": "feature exemple 2 ",
        "enabled": false
    }]
}
```



Exemple de fichier de configuration, `navigation.json` :

Dans notre cas l'exemple2 ne sera pas accessible et non visible dans le menu.

```json
{
    "text": "Rubrique exemple ",
    "visibleDansMenu": true,
    "visibleDansPlan": true,
    "submenu":
    [
        {
            "text": "exemple 1",
            "title": "exemple 1",
            "url": "/url_exemple1",
            "visibleDansMenu": true,
            "visibleDansPlan": true
        },
        {
            "featureFlipping": "features_exemple2",
            "text": "exemple2",
            "title": "exemple2",
            "url": "/url_exemple2",
            "visibleDansMenu": true,
            "visibleDansPlan": true
        }
    ]  
}
```

Lorsque tous les fichiers sont créés ou déjà présents, la configuration des fichiers `.json`  faits, il ne restera plus qu'à démarrer l'application.

# Annexe

## Fichier `src/middleware/featuresflipping.ts`


```javascript
// src/middleware/featuresflipping.ts
import { NavigationUtils } from 'hornet-js-components/src/utils/navigation-utils';
import { AbstractHornetMiddleware } from 'hornet-js-core/src/middleware/middlewares';
import { PageRouteInfos, RouteInfos, RouteType } from 'hornet-js-core/src/routes/abstract-routes';
import { PageRenderingMiddleware } from 'hornet-js-react-components/src/middleware/component-middleware';
import { HornetComponent } from 'hornet-js-react-components/src/widget/component/hornet-component';
import { HornetPage } from 'hornet-js-react-components/src/widget/component/hornet-page';
import { Utils } from 'hornet-js-utils';
import { HttpError } from 'hornet-js-utils/src/exception/http-error';
import { Logger } from 'hornet-js-utils/src/logger';
import { Class } from 'hornet-js-utils/src/typescript-utils';
import { CommonServiceData } from 'src/services/data/common-service-data';
import { CommonServiceDataImpl } from 'src/services/data/common-service-data-impl';
import { SecurityError } from "hornet-js-utils/src/exception/security-error";

import * as _ from 'lodash';
import * as React from 'react';
import * as ReactDOMServer from 'react-dom/server';

// -------------------------------------------------------------------------------------------------------------------
// // PageRenderingMiddleware
// -------------------------------------------------------------------------------------------------------------------
// //
const logger: Logger = Utils.getLogger('middleware.PageRenderingMiddleware');

export class FeaturePageRenderingMiddleware extends AbstractHornetMiddleware {
    private static logger: Logger = Utils.getLogger('hornet-js-react-components.middleware.PageRenderingMiddleware');

    private commonService: CommonServiceData = new CommonServiceDataImpl();

    constructor() {
        super((req, res, next) => {

            const routeInfos: RouteInfos = Utils.getCls('hornet.routeInfos');

            const pageRouteInfos = routeInfos as PageRouteInfos;

            // Patch pour interdire les accès à une feature desactivée
            this.commonService.getFeatures().then((response) => {
                const flips = {};
                (response.featureFlipping || []).forEach((f) => {
                    flips[ f.id ] = { ...f };
                });
                Utils.appSharedProps.set('feature.flipping', flips);
                logger.info('check feature-flippings', flips);
                const items = NavigationUtils.getConfigMenu();
                let ok = true;
                const fn = (menu: any[]) => {
                    menu.forEach((item) => {
                        if (flips[ item.featureFlipping ]
                            && flips[ item.featureFlipping ].enabled === false) {
                            item.visibleDansMenu = false;
                            item.visibleDansPlan = false;

                            if (req.url === item.url) {
                                logger.debug('feature disabled : ', req.url);
                                ok = false;
                            }
                        }

                        if (item.submenu && item.submenu.length > 0) {
                            fn(item.submenu);
                        }
                    });
                };


                fn(items);

                Utils.setCls('hornet.menuConfig', items || {});

                if (ok) {
                    next();
                } else {

                    let e = new SecurityError();
                    FeaturePageRenderingMiddleware.logger.trace("Tentative d'accès à une route non autorisée", req.url, e.reportId);
                    next(e);

                }
            });

        });
    }
}
```


## Fichier `src/actions/features-get-action.ts`


```javascript
// src/actions/features-get-action.ts
import { RouteActionService } from 'hornet-js-core/src/routes/abstract-routes';
import { Utils } from 'hornet-js-utils';
import { Logger } from 'hornet-js-utils/src/logger';
import { CommonServiceData } from "src/services/data/common-service-data";

const logger: Logger = Utils.getLogger('features.get');

export class FeaturesGetAction extends RouteActionService<any, CommonServiceData> {

    execute(): Promise<any> {
        return this.doExecute();
    }

    doExecute(): Promise<any> {
        return this.getService().getFeatures()
            .then((response) => {
                const flips = {};
                (response || []).forEach((f) => {
                    flips[ f.id ] = { ...f };
                });

                return flips;
            }).catch(() => {
                return {};
            });
    }

}
```


## Fichier `src/resources/featureflipping.json`

```json
/*src/resources/featureflipping.json*/

{
    "featureFlipping": [{
        "id": /*ID de votre features*/,
        "label": /*Libelle ou petit description de la feature*/,
        "enabled":/*Activation(true) ou desactivation(false)*/
    }]
}
```

## Fichier `src/routes/common-server-routes.ts`


```javascript
/* src/routes/common-server-routes.ts */

import { AbstractRoutes, DataRouteInfos, PUBLIC_ROUTE } from 'hornet-js-core/src/routes/abstract-routes';
import { FeaturesGetAction } from "src/actions/features-get-action";
import { StatusGet } from "src/actions/status-get-actions";
import { CommonServiceDataImpl } from "src/services/data/common-service-data-impl";
import { URL_FEATURES, URL_STATUS } from "src/utils/urls";


export default class CommonServerRoutes extends AbstractRoutes {
    constructor() {
        super();

        /* Route des datas */
        this.addDataRoute(URL_STATUS,
            () => new DataRouteInfos(StatusGet, null, CommonServiceDataImpl),
            PUBLIC_ROUTE,
            'get',
        );

        this.addDataRoute(URL_FEATURES,
            () => new DataRouteInfos(FeaturesGetAction, null, CommonServiceDataImpl),
            PUBLIC_ROUTE,
            'get',
        );
    }
}

```
