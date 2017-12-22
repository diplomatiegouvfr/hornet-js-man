# Configuration

## Fichier de configuration de l'application : default.json

L'ensemble de la configuration applicative du serveur NodeJS se situe dans le fichier default.json et log4js.json contenus dans les sources de l'application.

La configuration des logs serveur a été séparée du reste et se trouve dans le fichier dédié log4j.json : cf chapitre "Fichier de configuration des logs serveur de l'application".

### Configuration applicative

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
|contextPath| Context de l'application déployée|Par défaut vide|
|welcomePage|Page de démarrage de l'application|Passé en paramètre du ServerConfiguration|
|localeI18n|Langue de l'application par défaut|{"locale": "fr-FR", "lang": "fr"} |
|themeHost [Facultatif]|Url du CDN|Url d'accès défini dans le fichier default.json|

```json
{
  "contextPath": "applitutorieljs",
  "welcomePage": "/accueil",
  "localeI18n": {
    "locale": "fr-FR",
    "lang": "fr"
  }
  ...
}

```

### Configuration thème embarqué
 
| Paramètre | Description | Valeur |
|-----------|-------------|--------|
|themeName| Thème de l'application| hornet-themes-intranet |

### Configuration serveur

Cette partie contient l'ensemble du paramétrage spécifique aux exécutions réalisées coté serveur, ainsi que ses spécificités de démarrage.

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
|route|Route identifié pour l'affinité de session nodejs|js1|
|port|Port de démarrage du serveur|8888|
|portMonitor| Port de démarrage du monitoring|9999|
|keepAlive|Activation du mode HTTP KeepAlive|true|
|keepAliveTimeout|Timeout de maintien d'une connexion keep-alive|15000|
|maxConnections|Nombre maximal de connexions à l'instance nodejs|100|
|timeout|Timeout de connexion TCP|300000|
|uploadFileSize|Taille maximal d'upload de fichier|1000000|
|sessionTimeout|Timeout des sessions utilisateur|1800000|
|stackTraceLimit|Taille d'une stackTrace dans les logs|100|
|bodyParserLimit|Taille max que peut recevoir le body-parser|50mb|

```json
  "server": {
    "route": "js1",
    "port": 8888,
    "portMonitor": 9999,
    "keepAlive": true,
    "keepAliveTimeout": 15000,
    "maxConnections": 100,
    "timeout": 300000,
    "uploadFileSize": 1000000,
    "sessionTimeout": 1800000,
    "stackTraceLimit": 100,
    "bodyParserLimit": "50mb"
  }
```

### Configuration Cookie

Cette partie contient l'ensemble du paramétrage des cookies de session

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
|domain|Domain du cookie|null|
|path|Path du cookie|null|
|httpOnly|Cookie accessible uniquement par la couche HTTP et non accessible par du javascript|true|
|secure|Cookie accessible uniquement en https|false|
|alwaysSetCookie|Utilisation à chaque requête de l'entête 'set-cookie' dans les réponses http|false|

```json
  "cookie": {
    //"domain": null,
    //"path": null,
    "httpOnly": true,
    "secure": false
    //"alwaysSetCookie": false
  }
```


### Configuration de la sécurité

Ce bloc contient l'ensemble des paramètres destinés à la configuration de helmet.

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
|enabled|Activation de la sécurité| true |
|hpp|HTTP Parameter Pollution attacks| true |
|ienoopen|Middleware for IE security. Set `X-Download-Options` to noopen| true |
|noSniff| Keep clients from sniffing the MIME type| true |
|csp.enabled|Activation de CSP (Content Security Policy)| true |
|csp.connectSrc|Noms de domaine des différentes sources de connexion du site| ["'self'"]|
|csp.scriptSrc|Noms de domaine des différentes ressources de scripts du site| ["'self'", "'unsafe-inline'", "'unsafe-eval'"]|
|csp.styleSrc|Noms de domaine des différentes ressources de css du site | ["'self'", "'unsafe-inline'"]|
|csp.fontSrc|Noms de domaine des différentes ressources de fonts du site| ["'self'", {hostname}]|
|csp.imgSrc|Noms de domaine des différentes ressources d'images scripts du site | ["'self'", {hostname}]|
|csp.formAction|Noms de domaine des différentes formulaires d'authentification|["'self'"]|
|csp.reportOnly|Si valorisé à true, génération d'un rapport d'erreur uniquement|false|
|csp.setAllHeaders|Valorisé à true si tous les headers doivent être settés|true|
|csp.disableAndroid|Permet de désactiver la navigation via Android|false|
|csp.frameguard.enabled|Activation de frameguard|true|
|csp.frameguard.deny|Activation de frameguard|"deny"|
|csp.frameguard.allowFromPattern|Activation de frameguard|""|
|xss.enabled|Activation de la protection contre les failles XSS|true|
|xss.setOnOldIE|Force le header X-XSS-Protection sur les anciens IE|true|
|hpkp.enabled|Activation du Public Key Pinning: HPKP, HTTPS certificates can be forged, allowing man-in-the middle attacks|true|
|hpkp.maxAge|Durée de validation|7776000000|
|hpkp.sha256s|Liste des sha au format 256|["AbCdEf123=","ZyXwVu456="]|
|hpkp.includeSubdomains|Inclusion des sous domaines|true|
|hpkp.reportUri|Url de rapport|null|
|hpkp.reportOnly|Si valorisé à true, génération d'un rapport d'erreur uniquement|false|
|hsts.enabled|Activation du HTTP Strict Transport Security: hsts|false|
|hsts.maxAge|Durée de validation|7776000000|
|hsts.includeSubDomains|Inclusion des sous domaines|true|
|hsts.preload|Activation du preload dans le header HSTS|false|
|csrf.enabled|Activation du mode CSRF : Cross-Site Request Forgery|true|


```json
"security": {
    "enabled": true,
    "hpp": true,
    "ienoopen": true,
    "noSniff": true,
    "csp": {
      "enabled": true,
      "scriptSrc": [
        "'self'",
        "'unsafe-inline'",
        "'unsafe-eval'"
      ],
      "styleSrc":[
        "'self'",
        "[Protocol]://[host]:[port]",
        "'unsafe-inline'"
      ],
      "fontSrc":[
        "'self'",
        "[Protocol]://[host]:[port]"
      ],
      "imgSrc":[
        "'self'",
        "[Protocol]://[host]:[port]"
      ],
      "reportOnly": false,
      "setAllHeaders": true,
      "disableAndroid": false
    },
    "frameguard": {
      "enabled": true,
      "mode": "deny",
      "allowFromPattern": ""
    },
    "xss": {
      "enabled": true,
      "setOnOldIE": true
    },
    "hpkp": {
      "enabled": true,
      "maxAge": 7776000000,
      "sha256s": [
        "AbCdEf123=",
        "ZyXwVu456="
      ],
      "includeSubdomains": true,
      "reportUri": null,
      "reportOnly": false
    },
    "hsts": {
      "enabled": false,
      "maxAge": 10886400000,
      "includeSubDomains": true,
      "preload": false
    },
    "csrf": {
      "enabled": true
    }
  }
```

### Configuration des logs client

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
|remote|Activatino des remotes log|false|
|level|Niveau de log|INFO|

```json
  "logClient": {
    "remote": false,
    "level": "INFO",
    "appenders": [...]
  },
```

### Déclaration des appenders

Type BrowserConsole :


| Paramètre | Description | Valeur |
|-----------|-------------|--------|
|type|Type d'appender|BrowserConsole|
|layout.type| Type d'affichage des messages|THIN/BASIC/pattern/...|
|layout.pattern| Schéma d'affichage des messages |"%p|%c|%m%"|

```json
"appenders": {
    "BrowserConsole" : {
        "type": "BrowserConsole",
        "layout": {
          "type": "THIN"
        }
    }
}
```

Type Ajax :

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
|type|Type d'appender|Ajax|
|layout.type| Type d'affichage des messages|THIN/BASIC/pattern/...|
|layout.pattern| Schéma d'affichage des messages |"%p|%c|%m%"|
|threshold|Seuil d'envoi des messages de log|100|
|timeout|Timeout d'envoie des messages|3000|
|url|Url d'envoie des logs|/logs|

```json
"appenders": {
    "Ajax": {
        "type": "Ajax",
        "layout": {
          "type": "BASIC"
        },
        "threshold": 100,
        "timeout": 3000,
        "url": "/log"
    }
}
```

### Configuration des services


| Paramètre | Description | Valeur |
|-----------|-------------|--------|
|defaultServices.host| URL de déploiement du module applitutoriel-service| [Protocol]://[host]:[port] |
|defaultServices.name| Nom de déploiement des services|applitutoriel|

```json
  "defaultServices": {
    "host": "http://localhost:8080/",
    "name": "applitutoriel-service"
  },
```


### Mode mock

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
|enabled|Activation du mode mock de l'application|false|
|servicePage|Bouchon des services page| enabled : false|
|serviceData|Bouchon des services data |enabled : false|

```json
  "mock": {
    "enabled": false,
    "servicePage": {
      "enabled": false
    },
    "serviceData": {
      "enabled": false
    }
  }
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
### Configuration de l'authentification

Note : Il ne s'agit pas d'une configuration à proprement parlé de Hornet mais uniqument viable dans l'applitutoriel

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
|loginUrl|Url de connexion à l'application|/login|
|logoutUrl|Url de déconnexion à l'application|/logout|

```json
  "authentication": {
    "loginUrl": "/login",
    "logoutUrl": "/logout"
  }
```

### Configuration d'une base de données

Il est possible d'ajouter une configuration permettant de se connecter à une base de données.

Les configurations de base de données déclarées dans la configuration peuvent ensuite être utilisées dans une classe héritant de `HornetSequelizeModel` du projet `hornet-js-database`.

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
|database|Object contenant la liste des bases de données et leur configuration||
|configApplituto| Nom de la base de données avec sa configuration||
|uri|Chaine de connexion à une bdd [sgbd]://[user]@[host]:[ports]/[name]||
|options|Options nécessaires ou facultatives pour le sgbd||

```json
    "database": {
        "configApplituto": {
          "uri": "postgres://hornetjs@localhost:5433/applitutorielDB",
          "options" : {
            "pool" : {
              "max" : 5,
              "min" : 0,
              "idle" : 1000
            }
          }
        }
      }
```

### Configuration d'un serveur de mail

Pour configurer l'application afin d'utiliser un serveur de mail SMTP, vous pouvez utiliser une configuration avec les paramètres ci-dessous.

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
|config|Object de configuration pour nodeMailer|https://nodemailer.com/smtp/|
|mailRecever|Mail servant de destinataire à l'envoi de mail||

```json
"mail": {
    "config": {
      "host": "127.0.0.1",
      "port": 25,
      "secure": false,
      "connectionTimeout": 20000,
      "tls": {
        "rejectUnauthorized": false
      },
      "auth": {
        "user": "applituto",
        "pass": "applituto"
      }
    },
    "mailReceiver": "applituto@monmail.fr",
    "mailSender": "contact.applituto@monmail.fr"
  }
```

### Configuration du Cache

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
|enabled|Activation du cache sur les requêtes de services|true|
|timetolive|Durée de rétention du cache|60|

```json
"cache": {
    "enabled": true,
    "timetolive": 60
  }
```

## Configuration des logs : log4js.json

### Appender console

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
|type|Type d'appender|*console* Affiche des logs dans la console |console|
|layout.type| Type d'affichage des messages|pattern|
|layout.pattern| Schéma d'affichage des messages,  %[...] permet d'afficher les couleurs dans la console.|"%[%d{ISO8601}|%x{tid}|%x{user}|%p|%c|%x{fn}|%m%]"|


Ex: type console

```json
{
   "appenders": {
      "console": {
        "type": "console",
        "layout": {
          "type": "pattern",
          "pattern": "%[%d{ISO8601}|%x{tid}|%x{user}|%p|%c|%x{fn}|%m%]"
        }
      }
  }
}
```

### Appender dateFile

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
|type|Type d'appender|*dateFile* permet d'avoir un rolling avec un fichier contenant la date|dateFile|
|pattern| Présent pour les types *dateFile* <br />Permet de donner un pattern de date qui sera ajouté au nom du fichier.|-yyyy-MM-dd|
|filename| Chemin absolu ou relatif au lancement du fichier de log | /var/log/nodejs/#{INSTANCE_NAME}/#{INSTANCE_NAME}.log |
|layout.type| Type d'affichage des messages|pattern|
|layout.pattern| Schéma d'affichage des messages |"%d{ISO8601}|%x{tid}|%x{user}|%p|%c|%x{fn}|%m"|
|compress| Compression gzip lors du rotate |true|
|keepFileExt| Permet de garder le pattern de log d'origine, monappli-20171212.log.gz |true|


```json
{
   "appenders": {
      "dateFile": {
         "type": "dateFile",
         "pattern": ".yyyy-MM-dd",
         "filename":"/var/log/nodejs/#{INSTANCE_NAME}/#{INSTANCE_NAME}.log",
         "layout": {
            "type": "pattern",
            "pattern": "%d{ISO8601}|%x{tid}|%x{user}|%p|%c|%x{fn}|%m"
         },
         "compress": true,
         "keepFileExt": true
      }
   }
}
```

### Categories

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
|default|Nom d'une categorie d'appender|default|
|appenders|Liste des appenders utilisés |"dateFile", "console"|
|level|Level des appenders |INFO|

```json
{
   "categories": {
      "default": { "appenders": ["dateFile", "console"], "level": "INFO" }
   }
}
```