### Fichier de configuration de l'application : default.json

L'ensemble de la configuration applicative du serveur NodeJS se situe dans le fichier default.json et log4js.json contenus dans les sources de l'application.

La configuration des logs serveur a été séparée du reste et se trouve dans le fichier dédié log4j.json : cf chapitre "Fichier de configuration des logs serveur de l'application".

#### Configuration applicative

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
|contextPath| Context de l'applicatin déployé|Par défaut vide|
|welcomePage|Page de démarrage de l'application|Passé en paramètre du ServerConfiguration|
|themeUrl|Url du thème applicative|[Protocol]://[host]:[port]/hornet/themName|

```json
{
  "contextPath": "applitutoriel",
  "welcomePage": "/accueil",
  "themeHost": "[Protocol]://[host]:[port]",
  "themeUrl": "${themeHost}/5.0.X/default"
  ...
}

```

#### Configuration serveur

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
|route|Route identifié pour l'affinité de session nodejs|js1|
|port|Port de démarrage du serveur|8888|
|keepAlive|Activation du mode HTTP KeepAlive|true|
|maxConnections|Nombre maximal de connexion à l'instance nodejs|100|
|timeout|Timeout des réponses HTTP|300000|
|uploadFileSize|Taille maximal d'upload de fichier|1000000|
|sessionTimeout|Timeout des sessions utilisateur|1800000|

```json
  "server": {
    "route": "js1",
    "port": 8888,
    "keepAlive": true,
    "maxConnections": 100,
    "timeout": 300000,
    "uploadFileSize": 1000000,
    "sessionTimeout": 1800000
  }
```

#### Configuration Cookie

Cette partie contient l'ensemble du paramétrage spécifique aux exécutions réalisées coté serveur, ainsi que ses spécificités de démarrage.

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
|domain|Domain du cookie|null|
|path|Path du cookie|null|
|httpOnly|Activation du mode HTTP KeepAlive|true|
|secure|securisation du cookie|true|
|alwaysSetCookie|Ajout du cookie dans le Header|false|

```json
  "cookie": {
    //"domain": null,
    //"path": null,
    "httpOnly": true,
    "secure": false
    //"alwaysSetCookie": false
  }
```


#### Configuration de la sécurité

Ce bloc contient l'ensemble des paramètres destinés à la configuration de helmet.

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
|enabled|Activation de la sécurité| true |
|hpp|HTTP Parameter Pollution attacks| true |
|ienoopen|Middleware for IE security. Set `X-Download-Options` to noopen| true |
|noSniff| keep clients from sniffing the MIME type| true |
|csp.enabled|Activation de CSP (Content Security Policy)| true |
|csp.scriptSrc|noms de domaine des différentes ressources de scripts du site| ["'self'", "'unsafe-inline'", "'unsafe-eval'"]|
|csp.styleSrc|noms de domaine des différentes ressources de css du site | ["'self'", "'unsafe-inline'"]|
|csp.fontSrc|noms de domaine des différentes ressources de fonts du site| ["'self'", {hostname}]|
|csp.imgSrc|noms de domaine des différentes ressources d'images scripts du site | ["'self'", {hostname}]|
|csp.reportUri|noms de domaine des différentes ressources d'images scripts du site | ["'self'"]|
|csp.reportOnly|si valorisé à true, génération d'un rapport d'erreur uniquement|false|
|csp.setAllHeaders|valorisé à true si tous les headers doivent être settés|true|
|csp.disableAndroid|permet de désactiver la navigation via Android|false|
|xss.enabled|Activation de la protection contre les failles XSS|true|
|xss.setOnOldIE|Force le header X-XSS-Protection sur les anciens IE|true|
|hpkp.enabled|Activation du Public Key Pinning: HPKP, HTTPS certificates can be forged, allowing man-in-the middle attacks|true|
|hpkp.maxAge|Durée de validation|7776000000|
|hpkp.sha256s|Liste des sha au format 256|["AbCdEf123=","ZyXwVu456="]|
|hpkp.includeSubdomains|Inclusion des sous domaines|true|
|hpkp.reportUri|url de rapport|null|
|hpkp.reportOnly|si valorisé à true, génération d'un rapport d'erreur uniquement|false|
|hsts.enabled|Activation du HTTP Strict Transport Security: hsts|false|
|hsts.maxAge|Durée de validation|7776000000|
|hsts.includeSubDomains|Inclusion des sous domaines|true|
|hsts.preload|Activation du preload dans le header HSTS|false|
|csrf.enabled|Activation du mode CSRF : Cross-Site Request Forgery|true|
|csrf.maxTokensPerSession|Nombre de token par session|10|


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
      "enabled": true,
      "maxTokensPerSession": 10
    }
  }
```

#### Configuration des logs client

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
|remote|Activatino des remotes log|false|
|level|niveau de log|INFO|

```json
  "logClient": {
    "remote": false,
    "level": "TRACE",
    ...
```

#### Déclaration des appenders

Type BrowserConsole :


| Paramètre | Description | Valeur |
|-----------|-------------|--------|
|type|Type d'appender|BrowserConsole|
|layout.type| Type d'affichage des messages|THIN/BASIC/pattern/...|
|layout.pattern| Schéma d'affichage des messages |"%p|%c|%m%"|

```json
"appenders": [
{
	"type": "BrowserConsole",
	"layout": {
	  "type": "THIN"
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
|url|url d'envoie des logs|/logs|

```json
"appenders": [
	{
	    "type": "Ajax",
	    "layout": {
	      "type": "BASIC"
	    },
	    "threshold": 100,
	    "timeout": 3000,
	    "url": "/log"
	}
]
```

#### Configuration des services


| Paramètre | Description | Valeur |
|-----------|-------------|--------|
|services.host| URL de déploiement du module applitutoriel-service| [Protocol]://[host]:[port] |
|services.name| Nom de déploiement des services|applitutoriel|

```json
  "defaultServices": {
    "host": "http://localhost:8080/",
    "name": "applitutoriel-service"
  },
```

#### Mode mock

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
|enabled|Activation du mode mock de l'application|false|
|host|Host local du mock|localhost|
|routes|Chemin vers le fichier de routes mocké sans le /src |/mock/routes|

```json
  "mock": {
    "enabled": false,
    "host": "127.0.0.1", //default localhost
    "routes": "/mock/routes"
  }
```

#### Mode fullSPA

NOTE : Le mode fullSPA n'est pas encore complètement supporté par hornet, la configuration est présente à titre d'information

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
|enabled|Activation du mode fullSPA|false|
|host|Host du mode fullSPA||
|name|nom du service pour le mode fullSPA|/services|
|staticPath|prefixe des resources statiques pour le mode fullSPA||

```json
"fullSpa": {
    "enabled": false,
    "host": "",
    "name": "/services",
    "staticPath": ""
  }
```

Attention, en mode fullSpa, le fichier `/static/index.html` déclare le contexte applicatif en dur.
Ce contexte est utile pour constituer les requêtes vers le serveur web (pour charger le client js et récupérer la configuration notamment). La configuration n'étant pas encore disponible, on ne peut pas utiliser la propriété contextPath.

Par conséquent, en cas de changement du contextPath, il faut penser à le modifier dans index.html. 
Cette contrainte s'applique aussi pour l'option de paramétrage fullSpa.staticPath. Elle est vide par défaut, mais si elle est définie, il faut la reporter dans index.html (la concaténer à la suite du contexte). Exemple : 

```javascript
var contextAppli = "/applitutoriel/static";
```

#### Configuration de l'authentification

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

#### Configuration du Cache

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

### Fichier de configuration des logs serveur de l'application : log4js.json

Niveau de log :

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
|level.[all]|niveau de log pour toute l'application|INFO|
|level.monappli.view|niveau de log spécifique pour une partie de l'application |optionnel|

```json
 "log": {
    "levels": {
      "[all]": "DEBUG",
      "hornet-js-components.table": "TRACE"
    }
    ...
```

Déclaration des appenders :

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
|type|Type d'appender|*file* pour un fichier simple<br/>*dateFile* pour un fichier contenant la date<br/>*console* ...|
|filename| Chemin absolu ou relatif au lancement du fichier de log | /var/log/nodejs/applitutoriel/applitutoriel-1.log|
|pattern| Présent pour les types *dateFile* <br />Permet de donner un pattern de date qui sera ajouté au nom du fichier.|-yyyy-MM-dd|
|layout.type| Type d'affichage des messages|pattern|
|layout.pattern| Schéma d'affichage des messages |"%[%d{ISO8601}|%x{tid}|%x{user}|%p|%c|%x{fn}|%m%]"|


Ex: type console

```json
"appenders": [
	{
	    "type": "console",
	    "layout": {
	      "type": "pattern",
	      "pattern": "%[%d{ISO8601}|%x{tid}|%x{user}|%p|%c|%x{fn}|%m%]"
	    }
	}
]
```

ex : type fichier

```json
"appenders": [
	{
	    "type": "dateFile",
	    "filename": "log/app.log",
	    "layout": {
	      "type": "pattern",
	      "pattern": "%d{ISO8601}|%x{tid}|%x{user}|%p|%c|%x{fn}|%m"
	    }
	}
]
```