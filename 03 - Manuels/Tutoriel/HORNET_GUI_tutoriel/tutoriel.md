# Tutoriel Hornet

Objectif : 

- Créer premier écran Hornet

Support : 

- Applitutoriel Hornet

Fonctionnalité : 

- Ajouter la liste des produits par partenaire.

Etapes à réaliser :

- Ajout d'une icone dans la liste des partenaires pour accéder à la liste des produits de ce partenaire
- Ajout d'un écran pour afficher la liste des produits d'un partenaire

## Ajout du lien vers la nouvelle page

### Ajout d'une colonne à la liste des partenaires

Pour chaque partenaire, affichage d'une icône permettant d'accéder à sa liste de produit

#### Ajout de l'icône au tableau

Dans le fichier : `src/views/par-rpa-page.jsx`

```javascript
	var tableConfig = {
		name: appName,
		columns: {
	...
		supprimer: {
				title: "",
				render: this.getIconeSupprimer,
				hide: true,
				custom: true
			},
		afficherProduits: {
			title: "",
			render: this.getIconeAfficherProduits,
			hide: true,
			custom: true
		}
	...
		if (pageInformationsStore.userHasRole(Roles.ADMIN)) {
			tableConfig.columns.supprimer.hide = false;
			tableConfig.columns.afficherProduits.hide = false; // dans le cas d'un affichage uniquement aux adminsitrateurs 
		}
	....
		}
	};

	var appConfig = {
		name: appName,
		routes: {
...
			products: this.genUrl("/produits/consulter"),
		}
	};

```	

#### Ajout de la page de destination

Dans le fichier : `src/views/par-rpa-page.jsx`

```javascript
	var appConfig = {
		name: appName,
		routes: {
...
			products: this.genUrl("/produits/consulter"),
		}
	};

```	

Définition de l'icône du tableau

```	javascript
    getIconeAfficherProduits: function (value, item) {
        return (
            <Icon url={this.state.appConfig.routes.products  + "/" + item.id}
                  src={this.genUrlTheme("/img/tableau/show.png")}
                  alt={ this._formatIconLabel(this.state.i18n.tableau.iconAfficherProduits, item)}
                  title={ this._formatIconLabel(this.state.i18n.tableau.iconAfficherProduits, item)}
                  onClick={this._onClickTable.bind(this)}
            />
        );
    },
```	

#### Ajout des traductions

Dans les fichiers :

- `src/views/messages.json`
- `src/views/messages-fr-FR.json`


```json
  "partenairesListePage": {
    "tableau": {
      "iconConsulter": "Consulter la fiche de {nom} {prenom}",
      "iconAfficherProduits": "Consulter les produits de {nom} {prenom}",
      },
  },
```

## Création d'un écran

### Ajout de la route pour afficher la nouvelle page

Dans le fichier : `src/routes/routes.ts`

Ajout des références pour la nouvelle fonctionnalité

```javascript
...
        match.lazy("/produits", "pro/pro-lsp-routes");
...
    buildDataRoutes(match:routerInterfaces.MatchFn) {
...
        match.lazy("/produits", "pro/pro-lsp-routes");
    }
```

Ajout du fichier : `src/routes/pro/pro-lsp-routes.ts`

```javascript
"use strict";
import IRoutes = require("hornet-js-core/src/routes/router-interfaces");
import routerNs = require("hornet-js-core/src/routes/router-view");
import utils = require("hornet-js-utils");
import MediaType = require("hornet-js-core/src/protocol/media-type");
import fvaNS = require("hornet-js-core/src/actions/form-validation-action");
import Roles = require("src/utils/roles");
import Action = require("hornet-js-core/src/actions/action");
import ActionsChainData = require("hornet-js-core/src/routes/actions-chain-data");
import RedirectClientAction = require("hornet-js-core/src/actions/redirect-client-action");
import SimpleAction = require("hornet-js-core/src/actions/simple-action");
import routerInterfaces = require("hornet-js-core/src/routes/router-interfaces");

var listeProduitsPage = require("src/views/pro/pro-lsp-page");

var _ = utils._;
var logger = utils.getLogger("applitutoriel.routes.pro.pro-lsp-routes");

class ProduitRoutes implements IRoutes.IRoutesBuilder {

    buildViewRoutes(match:IRoutes.MatchViewFn) {
        logger.info("Initialisation des routes view de la page listeProduitsPage");
        match("/", (context, id) => {
            logger.info("listeProduitsPage ROUTER VIEW, partenaire : ", id);
            return {
                composant: listeProduitsPage,
                roles: Roles.EVERYONE
            };
        });

        match("/consulter/:id", (context, id) => {
            logger.info("listeProduitsPage consulter ROUTER VIEW, partenaire :", id);

            return {
                composant: listeProduitsPage,
                roles: Roles.EVERYONE
            };
        });
    }
}
export = ProduitRoutes;
```

### Ajout de la vue de la nouvelle page 

Ajout du fichier : `src/view/pro/pro-lsp-page.tsx`

```javascript
var React = require("react");
import HornetComponent = require("hornet-js-components/src/hornet-component");
import utils = require("hornet-js-utils");

var logger = utils.getLogger("applitutoriel.views.pro.pro-lsp-page");

@HornetComponent.ApplyMixins()
@HornetComponent.Error()
class ListeProduitsPage extends HornetComponent<any,any> {

     constructor(props?:any, context?:any) {
        super(props, context);

        logger.info("ListeProduitsPage constructor BEGIN");

        var intlMessages = this.i18n("produitsPage");

        var appName = "PRODUITS";

        var appConfig = {
            name: appName,
            routes: {
            }
        };

        this.state =  {
            i18n: intlMessages,
            appConfig: appConfig,
            isOpenAlertDelete: false,
            itemToDelete: null,
            resetStoreOnRedirect: true
        };

        logger.info("ListeProduitsPage constructor END");
    };
	
    @HornetComponent.AutoBind
    onChange() {
        this.forceUpdate();
    };

    render() {
       logger.info("ListeProduitsPage render");

        return (
            <div>
				<h2>HELLO WORLD  !!!!!!!!!!</h2>
            </div>
        );
    };
}

export = ListeProduitsPage;
```

> Vérifier l'affichage de l'icône dans le tableau

### Ajout du service 

Service permettant de récupérer la liste des produits d'un partenaire.

Modification du fichier : `ProduitController.java`

```javascript
...
import org.springframework.web.bind.annotation.PathVariable;
...

    @RequestMapping(value = "/consulter/{id}", method = RequestMethod.GET)
    public List<Produit> listerProduits(@PathVariable final Long id) {

        final List<Produit> produits = produitService.getListeByPartenaire(id);
        
        return produits;
    }
```

> Vérifier la réponse du service en [testant son url](http://localhost:8080/applitutoriel-service/produits/consulter/1)


### Ajout de l'action pour contacter le service de la nouvelle fonctionnalité

Nous avons besoin de contacter le service externe permettant de récupérer les informations

Ajout du fichier : `src/actions/pro/pro-lsp-actions.ts`

```javascript

"use strict";
import utils = require("hornet-js-utils");
import Action = require("hornet-js-core/src/actions/action");
import ActionsChainData = require("hornet-js-core/src/routes/actions-chain-data");
import ProduitsApi = require("src/services/pro/pro-lsp-api");
import N = require("hornet-js-core/src/routes/notifications");

var logger = utils.getLogger("applitutoriel.actions.pro.pro-lsp-actions");
var WError = utils.werror;

export class ListerProduit extends Action<ActionsChainData> {
    execute(resolve, reject) {
        logger.info("ACTION ListerProduit - Appel API : ProduitApi.lister - Dispatch SECTEUR_RECEIVE_LIST");
        this.getInstance(ProduitsApi).lister(this.payload.id).then((retourApi:ActionsChainData) => {
            logger.trace("retour API : ", retourApi.result);

            this.actionContext.dispatch("PRODUITS_RECEIVE_RESULTS", retourApi.result);
            resolve(retourApi);
        }, (error) => {
            logger.trace("Retour en erreur", error);
            reject(new WError(error, this.actionContext.i18n("error.message.ER-AD-ESE-02")));
        });
    }
}
```

### Ajout du service pour contacter le service de la nouvelle fonctionnalité

Ajout du fichier : `src/services/pro/pro-lsp-api.ts`

```javascript
"use strict";
import ServiceApi = require("hornet-js-core/src/services/service-api");
import utils = require("hornet-js-utils");
import Promise = require("bluebird");
import ActionsChainData = require("hornet-js-core/src/routes/actions-chain-data");

var logger = utils.getLogger("applitutoriel.services.pro.pro-lsp-api");
var WError = utils.werror;
var _ = utils._;

class ProduitsApi extends ServiceApi {
    lister(id) {
        logger.info("SERVICES - lister");

        var url = this.buildUrl("/produits/consulter/" + id);
        //if (!utils.isServer) {
            //path = path + "/" + data;
        //}

        return new Promise((resolve:(n: ActionsChainData) => void, reject) => {
            logger.debug("Demande de listing des produits sur le serveur", url);
            this.request()
                .get(url) //
                .end(this.endFunction(resolve, reject
                    , "Listing des produits id :" + id));
        });
    }
}
export = ProduitsApi;
```

### Ajout de la route pour le service

Ajouter un buildDataRoutes dans le fichier : `src/routes/pro/pro-lsp-routes.ts`

```javascript
...

import ProduitsActions = require("src/actions/pro/pro-lsp-actions");
var ListeProduitsPage = require("src/views/pro/pro-lsp-page");

var _ = utils._;
var logger = utils.getLogger("applitutoriel.routes.pro.pro-lsp-routes");

class ProduitRoutes implements IRoutes.IRoutesBuilder {

    buildViewRoutes(match:IRoutes.MatchViewFn) {

        logger.info("Initialisation des routes view de la page ListeProduitsPage");


        match("/", (context, id) => {
            logger.info("ListeProduitsPage ROUTER VIEW, partenaire : ", id);
            return {
                actions: [new ProduitsActions.ListerProduit()],
                composant: ListeProduitsPage,
                roles: Roles.EVERYONE
            };
        });

        match("/consulter/:id", (context, id) => {
            logger.info("ListeProduitsPage consulter ROUTER VIEW, partenaire :", id);

            var actions = [new ProduitsActions.ListerProduit().withPayload({
                id: id
            })];

            return {
                actions: actions,
                composant: ListeProduitsPage,
                roles: Roles.EVERYONE
            };
        });
    }

    buildDataRoutes(match:IRoutes.MatchDataFn) {
        logger.info("Initialisation des routes data de la page ListeProduitsPage");

        match("/", (context, id) => {
            logger.info("ListeProduitsPage ROUTER VIEW, partenaire : ", id);
            return {
                actions: [new ProduitsActions.ListerProduit()],
                composant: ListeProduitsPage,
                roles: Roles.EVERYONE
            };
        });

        match("/consulter/:id", (context, id) => {
            logger.info("ListeProduitsPage consulter ROUTER VIEW, partenaire :", id);

            var actions = [new ProduitsActions.ListerProduit().withPayload({
                id: id
            })];

            return {
                actions: actions,
                composant: ListeProduitsPage,
                roles: Roles.EVERYONE
            };
        });
    }
}
export = ProduitRoutes;
```

### Modification du dispatcher

Ajout du nouveau store dans le dispatcher dans le fichier : `src/dispatcher/app-dispatcher.ts`

```javascript
...
import ProduitsStore = require("src/stores/pro/pro-lsp-store");

class AppDispatcher extends GenericDispatcher{

    constructor(componentActionErrorHandler?:Function){
...
        this.dispatcher.registerStore(ProduitsStore);
    }
}

export = AppDispatcher;
```

### Ajout du store de la nouvelle page

```javascript
"use strict";
import utils = require("hornet-js-utils");
var _ = utils._;

import ActionsChainData = require("hornet-js-core/src/routes/actions-chain-data");
import ITableStore = require("hornet-js-components/src/table/store/table-store-interface");

var logger = utils.getLogger("applitutoriel.stores.pro.pro-lsp-store");

import BaseStore = require("fluxible/addons/BaseStore");

class ProduitsStore extends BaseStore implements ITableStore{

    static storeName:string = "ProduitsStore";

    private produits:Array<any>;


    static handlers:any = {
        "PRODUITS_RECEIVE_RESULTS": function (res) {
            logger.trace("PRODUITS_RECEIVE_RESULTS liste", res);
            this.produits = {
                items: res,
                nbTotal: res.length
            };
            this.filters = {};
            this.tableVisible = true;
            this.emitChange();
        }
    };

    constructor(dispatcher) {
        super(dispatcher);
        this.produits = new Array();
    }

    private updateData(produits:Array<any>) {
        this.produits = produits;
        this.emitChange();
    }

    getListProduits():Array<any> {
        logger.trace("Récupération des produits.");
        return this.produits;
    }

    getAllResults(key:string):any {
        logger.trace("Récupération des produits");
        return this.produits;
    }

    getFilters(key:string):any {
        return null;
    }

    getCriterias(key:string){
        return null;
    }

    rehydrate(state:any) {
        logger.debug("ProduitStore rehydrate");
        this.produits = state.secteurs;
    }

    dehydrate():any {
        logger.debug("ProduitStore dehydrate");
        return {
            produits: this.produits
        };
    }
}

export = ProduitsStore;
```

### Modification de la vue pour afficher le tableau de données 

Dans le fichier : `src/views/pro-lsp-page.jsx`

```javascript
var React = require("react");
import HornetComponent = require("hornet-js-components/src/hornet-component");
import utils = require("hornet-js-utils");
//import ProLspProps = require("src/views/pro/pro-lsp-page-props");

var logger = utils.getLogger("applitutoriel.views.pro.pro-lsp-page");

var Tableau = require("hornet-js-components/src/table/table");
import ProduitsStore = require("src/stores/pro/pro-lsp-store");

@HornetComponent.ApplyMixins()
@HornetComponent.Error()
class ListeProduitsPage extends HornetComponent<any/*ProLspProps*/,any> {

    static storeListeners = [ProduitsStore];

     constructor(props?:any, context?:any) {
        super(props, context);

        logger.info("ListeProduitsPage constructor BEGIN");

        var intlMessages = this.i18n("produitsPage");

        var appName = "PRODUITS";

        var tableConfig = {
            name: "PRODUITS",
            columns: {
                id: {
                    title: intlMessages.id,
                    sort: "text"
                },
                nom: {
                    title: intlMessages.nom,
                    sort: "text"
                },
                desc: {
                    title: intlMessages.desc,
                    sort: "text"
                },
                secteur: {
                    title: intlMessages.secteur,
                    sort: "text",
                    render: this.getDescSecteur
                }
            },
            messages: {
                tableTitle: intlMessages.tableTitle,
                emptyResult: intlMessages.emptyResult,
                captionText: intlMessages.captionText,
                addTitle: intlMessages.addTitle
            },
            store: ProduitsStore,
            options : {
            },
            routes : {
            }
        };

        var appConfig = {
            name: appName,
            routes: {
            }
        };

        this.state =  {
            i18n: intlMessages,
            i18nCalendar: this.i18n("calendar"),
            i18nForm: this.i18n("form"),
            items: (this.getStore(ProduitsStore)).getAllResults(),
            //form: this._initNewForm(isModeCreation, intlMess.form),
            //formClass: PartenaireForm(this.getStore(SecteurStore), intlMess.form),
            appConfig: appConfig,
            tableConfig: tableConfig,
            isOpenAlertDelete: false,
            itemToDelete: null,
            resetStoreOnRedirect: true
        };

        logger.info("ListeProduitsPage constructor END");
    };
    @HornetComponent.AutoBind
    onChange() {
        this.forceUpdate();
    };

    render() {
       logger.info("ListeProduitsPage render");

        return (
            <div>
                <h2>{this.state.i18n.titre}</h2>

                <Tableau
                    config={this.state.tableConfig}
                    isVisible={true}
                />

                <a className="hornet-button" href={this.genUrl("/partenaires/cancel")} id="Cancel" title="Retour">Retour</a>

            </div>
        );
    };

    private getDescSecteur(secteur) {
        logger.error("getDescSecteur", secteur);
        return secteur.desc;
    };

}

export = ListeProduitsPage;
```

### Ajout des traductions

Dans les fichiers :

- `src/views/messages.json` 
- `src/views/messages-fr-FR.json`

```json
  "produitsPage": {
    "titre" : "liste des produits du partenaire",
    "id" : "identifiant",
    "nom" : "nom",
    "desc" : "description",
    "secteur" : "secteur",
    "tableTitle": "Adminitration des produits",
    "captionText": "Liste des produits du partenaire",
    "emptyResult": "Aucun produit présent",
    "addTitle": "Ajouter un produit"
  },
```
