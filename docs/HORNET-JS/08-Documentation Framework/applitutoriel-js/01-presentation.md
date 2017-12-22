# Applitutoriel-js

L'applitutoriel a pour objectif de présenter une application basée sur le framework Hornet.

A noter :
* Hornet facilite la mise en oeuvre du RGAA V3 dans une application.
* Mais l'utilisation de Hornet ne garantit pas qu'une application soit valide RGAA.

__Cas fonctionnels__

Les cas fonctionnels présentés dans l'application sont :

* Formulaire de recherche
* Présentation du résultat sous forme de tableau éditable
* Formulaire étendu
* Tableau d'ajout/suppression/modification d'items
* Affichage de graphique

__RGAA V3__

L'applitutoriel est une mise en pratique du RGAA V3 au travers du framework Hornet.

## Prérequis

* NodeJS 6.10
* hornet-js-builder 1.X installé en global:

### Installation du Hornet Builder

Se positionner dans le répertoire du projet `hornet-js-builder`

```shell
    npm install -g
```

Le builder peut aussi être installé directement avec npm:

```shell
    npm install -g hornet-js-builder
```

il faudra alors créer des liens symboliques vers le builder:

```
cd ~/bin
	ln -s ~/Dev/node-v6.10.0-linux-x64/bin/hb
	ln -s ~/Dev/node-v6.10.0-linux-x64/bin/hornetbuilder
```

* checkout du projet `applitutoriel-js`

## Initialisation
Se positionner dans le répertoire du projet `applitutoriel-js` et lancer la commande:

```shell
    hb install
```

Tips : la commande hb install -f permet de forcer l'installation si elle avait déjà été faite et qu'il y a besoin de prendre en compte de nouvelle version de dépendance.

Une fois l'application installée, celle-ci peut être compilée :

```shell
    hb compile
```

## Démarrage de l'application en mode développement

### Commande par défaut

la commande à exécuter en mode développement est la suivante:

```shell
    hb w
```

Le saviez-vous ?
La commande hb w réalise également les tâches d'installation et de compilation de l'application.

Elle permet de lancer l'application en mode `watcher` afin que les modifications soient prises en compte (ce qui
entrainera un redémarrage du serveur node dans le cas d'une détection de modification).

### Options

Il est également possible d'ajouter à cette commande l'option:

```shell
    hb w -i
```

Cette commande indique au builder de ne pas transpiler les fichiers typescript en javascript.
Elle est à utiliser dans le cas où l'IDE a été configuré de telle sorte que la transpilation ts->js
se fasse via ce dernier.

Voir la documentation du [builder](/hornetshowroom/composant/page/hornet-js-builder/readme).


## Vérification

L'application est accessible depuis un navigateur à l'addresse : `http://localhost:8888/applitutorieljs/`
où __applitutorieljs__ correspond au `contextPath` dans le fichier `config/default.json`.


## Mode Mock

Il est possible d'utiliser l'applitutoriel sans déployer la partie service.
Pour cela, activer le mode `mock` dans le `config/default.json`.

```json
  "mock": {
    "enabled": true,
    "servicePage": {
      "enabled": false
    },
    "serviceData": {
      "enabled": false
    }
  },
```

Injection des services page à définir dans le client.ts de votre application.

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

Injection des services data à définir dans le server.ts de votre application.

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
## Packaging de l'application

```shell
    hb package
```

Les livrables sont à récupérer dans le répertoire : `target`

- `applitutoriel-5.1.X-static.zip`
- `applitutoriel-5.1.X-dynamic.zip`
- `applitutoriel-5.1.X-environment.zip`