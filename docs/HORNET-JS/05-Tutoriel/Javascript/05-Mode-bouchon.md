# Lancement de l'application en mode bouchon

Exemple: applitutoriel en mode bouchon.

Le mode bouchon existe pour ne pas appeler le serveur  hornet-service distant. A sa place, l'application web, qui tourne sous node.js, va servir des données bouchonnées.


## Principes

Une fois le mode bouchon activé (définis sous la clé `mock.enabled` dans le fichier de configuration `default.json`), il faut definir les parties que l'on souhaite mocker : Soit les servicesPage soit les servicesData.


Rappels des clés à modifier:

|nom de la clé|exemple de valeur| Description|
|-------------|-----------------|------------|
|mock.enable|true|`true` Activer le mock de l'application|
|mock.servicePage.enabled|true| Bouchon des services pages |
|mock.serviceData.enabled|true| Bouchon des services data|


## Configurer un lancement
Le fichier de configuration, `default.json`, doit être modifié:

* Fichier en mode bouchonner  (**mock**):

```json

  "mock": {
     "enabled": true,
     "servicePage": {
       "enabled": true
     },
     "serviceData": {
       "enabled": false
     }
   }

```

Injection des services page à definir dans le client.ts de votre application

```javascript
if (Utils.config.getOrDefault("mock.enabled", false) && Utils.config.getOrDefault("mock.servicePage.enabled", false)) {
    // Mock des services Page
    Injector.register(AdministrationSecteurService, SecteurServicePageMockImpl);
    Injector.register(FichePartenairePageService, FichePartenaireServicePageMockImpl);
    Injector.register(RecherchePartenaireService, PartenaireServicePageMockImpl);

} else {
    Injector.register(AdministrationSecteurService, SecteurServiceImpl);
    Injector.register(FichePartenairePageService, FichePartenaireServiceImpl);
    Injector.register(RecherchePartenaireService, RecherchePartenaireServiceImpl);
}
```

Injection des services data à definir dans le server.ts de votre application

```javascript
if (Utils.config.getOrDefault("mock.enabled", false) && Utils.config.getOrDefault("mock.serviceData.enabled", false)) {
    // Mock des serviceData
    Injector.register(AuthService, AuthServiceDataMockImpl, Scope.SINGLETON);
    Injector.register(ApplitutorielSecteursServiceImpl, SecteurServiceDataMockImpl);
    Injector.register(PartenaireService, PartenaireServiceDataMockImpl);
    Injector.register(ReferentielPaysService, ReferentielPaysServiceDataMockImpl);

} else {
    Injector.register(AuthService, AuthServiceImpl, Scope.SINGLETON);
    Injector.register(ApplitutorielSecteursServiceImpl, SecteurServiceImpl);
    Injector.register(PartenaireService, PartenaireServiceImpl);
    Injector.register(ReferentielPaysService, ReferentielPaysServiceImpl);
    Injector.register(FichePartenaireService, FichePartenaireServiceImpl);
}
```
Chargement de la configuration dans votre page (hornet-layout.tsx).
```html
            if (!configMock || !configMock.enable) {
                configMock = Utils.config.getOrDefault("mock", {
                    enabled: false, sevicePage: { enabled: false },
                    seviceData: { enabled: false }
                })
            }
            return (
                <html dir="ltr" lang={Utils.getCls("hornet.internationalization").lang}>
                    <head>
                        ....
                    </head>
                    <body>
                       ...
                        <script dangerouslySetInnerHTML={{ __html: (this.state.state || "").toString() }} />
                        <script
                            dangerouslySetInnerHTML={{
                                __html: `window.Config["mock"] = ` + JSON.stringify(configMock) + `; window.Config["env"] = ` + process.env.NODE_ENV
                            }}
                        />
                       ...
                    </body>
                </html>
            );
```


## Créer un nouveau bouchon service page

Dans notre environnement et dans l'application tutoriel, il suffit de creer un nouveau service qui renvoit les données mockées
Voici une implementation de servicePage:
```javascript
/**
 * Implementation des services pour les secteurs
 * @class
 * @implements {AdministrationSecteurService}
 * @extends {ServiceApi}
 */
export class SecteurServiceImpl extends ServicePage implements AdministrationSecteurService {

    /**
     * liste tous les secteurs
     * @return Promise
     */
    lister() : Promise<any> {
        logger.trace("SERVICES - lister");
        return this.fetch({method : "get", url : this.buildUrl(URL_SECTEURS)});
    }
}
```
voici son service mocké

```javascript
/**
 * mock des services pour les secteurs
 * @class
 * @implements {AdministrationSecteurService}
 * @extends {ServiceApi}
 */
export class SecteurServicePageMockImpl extends ServicePage implements AdministrationSecteurService{

    /**
     * liste tous les secteurs
     * @return Promise
     */
    lister(): Promise<any> {
        logger.trace("SERVICES - lister");
        return Promise.resolve({
                                   "id":1,
                                   "dateCreat":1327143600000,
                                   "auteurCreat":"user",
                                   "dateMajEnreg":1331809200000,
                                   "auteurMajEnreg":"user",
                                   "dateSupprEnreg":null,
                                   "auteurSupprEnreg":null,
                                   "nom":"Agro-alimentaire",
                                   "desc":"Secteur de l'agro-alimentaire"
                               },
                               {
                                   "id":3,
                                   "dateCreat":1304762400000,
                                   "auteurCreat":"user",
                                   "dateMajEnreg":1331895600000,
                                   "auteurMajEnreg":"user",
                                   "dateSupprEnreg":null,
                                   "auteurSupprEnreg":null,
                                   "nom":"Electro-ménager",
                                   "desc":"Secteur de l'électro-ménager"
                               });
    }
}
```
Enfin il suffit de configuration de l'injector dans le client.ts de votre application pour utiliser notre service mocké

```javascript
if (Utils.config.getOrDefault("mock.enabled", false) && Utils.config.getOrDefault("mock.servicePage.enabled", false)) {
    // Mock des services Page
    Injector.register(AdministrationSecteurService, SecteurServicePageMockImpl);
} else {
    Injector.register(AdministrationSecteurService, SecteurServiceImpl);
}
```

