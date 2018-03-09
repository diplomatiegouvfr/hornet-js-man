# Les composants de sécurité

## Présentation

Ce chapitre détaille les solutions et bonnes pratiques du framework hornet pour prévenir les principales failles de sécurité web.

## Les attaques CSRF

### Définition

CSRF ou plus communément appelée "Cross Site Request Forgery" est une classe d'attaques propre aux applications web.

Elle reste l'une des moins médiatiques malgré les effets dévastateurs qu'elle peut présenter. Issue d'un problème nommé "Confused deputy" [1] découvert par Norm Hardy en 1988, l'attaque relative aux applications Web fut officiellement révélée en Juin 2001 par Peter Watkins sur la liste de diffusion "Bugtrack".

Elle fut surnommée "Cross Site Request Forgery" mais est plus communément appelé CSRF (prononcée Sea Surf) ou XSRF.

Cette technique exploite la confiance qu'une application montre à l'égard de ses clients. Le but est de forcer le navigateur de la victime à envoyer une requête silencieuse à l'insu d'un internaute. Au premier abord, cette attaque peut sembler difficile à mettre en place. Détrompez-vous! Elle peut être menée par n'importe qui et d'une manière extrêmement simple. Pour cela il suffit d'insérer un script dans une page web ou de le camoufler dans un e-mail. En suivant le lien contrefait, le navigateur de la victime va exécuter une requête vers un site sur lequel la victime est authentifiée.

Méconnue par la plupart des RSSI, elle vise particulièrement les sites web dont les structures des requêtes utilisées sont prédictibles.

### Exemple d'attaque

Il suffit de poster un formulaire provenant d'une fausse page web avec tous les champs requis.

### La parade

La parade est heureusement assez simple. Il suffit de générer un token coté serveur et de l'envoyer au client.

Du coté client le token est renvoyé au moment du POST du formulaire. Enfin le serveur peut verifier si son token est le même.

Techniquement :

- Le middleware express `Csrf` enrichie l'objet `request` afin de proposer une méthode générant un token et l'ajoutant dans la session.
- Le `router-view` utilise cette méthode pour retourner le token en session au client lors du rendu d'une page.
- Le navigateur récupère ce token au chargement de la page et l'envoi ensuite dans chaque reqûete Ajax effectuée (dans le header `x-csrf-token` ou dans le champ `x-csrf-token` du corps de la requête).
- Le middleware express `Csrf` intercepte tous les `POST`, `PUT`, `PATCH`, `DELETE`
- Le middleware vérifie que le token en session et le token dans l'entête de la requête sont bien identiques. Si c'est le middleware délègue ensuite le traitement au middleware suivant. Par contre si le token est invalide, une erreur 417 est envoyée et aucun middleware n'est ensuite appelé.

### Activer la securité csrf dans hornet

Pour activer la sécurité `csrf`, il faut positionner `enabled` à true dans le fichier de configuration.

```json
...
"security": {
  "csrf": {
    "enabled": true
  },
},
...
```

## La protection des paramètres en get: HTTP Parameter Pollution attacks

### Définition

L'ajout de paramètres à des requêtes GET (exemple: user?name=John&name=Johnny) peuvent provoquer des comportements inattendus.

La librairie 'hpp' propose un middleware express qui fiabilise ce comportement.

La page github de cette librairie présente le concept de l'attaque ainsi que la mise en œuvre pour y remédier: https://github.com/analog-nico/hpp

### Activer la securité des paramètres GET

Pour activer cette sécurité, il faut positionner `hpp` à true dans le fichier de configuration.

```json
...
"security": {
    "hpp": true
},
...
```

## La protection des requetes http avec httpOnly et secure
 
Le cookie `HTTPOnly` est utilisé pour conserver les informations d’authentification afin de protéger ces dernières contre les attaques XSS.

Le cookie secure est utilisé pour prévenir la lecture des cookies par une personne non authorisée, la requête nécessite alors d'être en https.
 
```json
...
"cookie": {
  "httpOnly": true,
  "secure": false
  ...
},
...
```

Autres options du cookie :
 
```json
"cookie": {
  //"domain": null,
  //"path": null,
  "httpOnly": true,
  "secure": false
  //"alwaysSetCookie": false
},
```

## Protection Helmet

### Définition

Plusieurs headers HTTP peuvent être ajoutés aux pages afin d'améliorer la sécurité au sein des navigateurs. Hornet utilise la librairie `helmet` qui regroupe plusieurs utilitaires.

### Les attaques

La description des attaques et leur parade sont expliquées sur le site de la librairie `helmet`: https://github.com/helmetjs/helmet

### Activer la sécurité des headers dans hornet

```json
...
"security": {
    "csp": {
    	enable: true
    	...
    },
    ...
},
...
```

Liste des plugins de sécurité helmet : 
- `contentSecurityPolicy` for setting Content Security Policy
- `frameguard` to prevent clickjacking
- `hidePoweredBy` to remove the X-Powered-By header
- `hpkp` for HTTP Public Key Pinning
- `hsts` for HTTP Strict Transport Security
- `ieNoOpen` sets X-Download-Options for IE8+
- `noCache` to disable client-side caching
- `noSniff` to keep clients from sniffing the MIME type
- `xssFilter` adds some small XSS protections

## Description complète de la Sécurité Hornet

Liste des élements de configuration :

| Paramètre | Description | Valeur par défaut |
|-----------|-------------|--------|
|enabled|Activation de la sécurité| `true`|
|hpp|HTTP Parameter Pollution attacks| `true`|
|ienoopen|Middleware for IE security. Set X-Download-Options to noopen| `true`|
|noSniff| Keep clients from sniffing the MIME type| `true`|
|csp.enabled|Activation de CSP (Content Security Policy)| `true`|
|csp.baseUri|URI dont le user-agent peut utiliser dans le document base Url| `["'self'"]`|
|csp.blockAllMixedContent|Bloque les éventuelles URL en http alors que l'appication est en https | `true`|
|csp.childSrc|Définit une source valide pour les webworker ou bien les contexts de navigation comme les "iframe" | `["'self'"]`|
|csp.connectSrc|Définit une source valide pour a récupértion de donnée XMLHttpRequest, WebSocket et EventSource | `["'self'"]`|
|csp.fontSrc|Noms de domaine des différentes ressources de fonts du site| `["'self'"]`|
|csp.formAction|Définit une cible valide pour les formulaires| `["'self'"]`|
|csp.frameAncestors|Définit un parent valide pour une frame ou une iframe| `["'self'"]`|
|csp.imgSrc|Noms de domaine des différentes ressources d'images scripts du site | `["'self'"]`|
|csp.manifestSrc|Définit le manifest qui doit être appliqué à la ressource| `["'self'"]`|
|csp.mediaSrc|Définit une source audio ou vidéo valide | `["'self'"]`|
|csp.objectSrc|Définit un source valide pour les object : embed, ou bien les applets| `["'self'"]`|
|csp.pluginTypes|Définit la liste des plugins invoqué par le navigateur| |
|csp.referrer|Définition de l'information "referrer" dans l'entête de requête| `"origin-when-cross-origin"`|
|csp.reflectedXss|Control les fonctionnalités du navigateur contre les attaques XSS| `"block"`|
|csp.reportUri|Noms de domaine des différentes ressources d'images scripts du site | `["'self'"]`|
|csp.sandbox|Applique des restrictions sur les actions au sein d'une page | `false`|
|csp.scriptSrc|Définition de sources valides pour le javascript| `["'self'", "'unsafe-inline'", "'unsafe-eval'"]`|
|csp.styleSrc|Définition de sources valides pour les styles | `["'self'", "'unsafe-inline'"]`|
|csp.upgradeInsecureRequests|Force les requêtes http à https quand la page est chargé en https | `false`|
|csp.reportOnly|Si valorisé à true, génération d'un rapport d'erreur uniquement| `false`|
|csp.setAllHeaders|Valorisé à true si tous les headers doivent être settés| `true`|
|csp.disableAndroid|Permet de désactiver la navigation via Android| `false`|
|frameguard.enabled|Activation du pluging de sécurité pour la prévention des 'Clickjacking attacks'| `true`|
|frameguard.action|Action CORS ( `'deny'`ou `'sameorigin'` ou `'allow-from'`)| `'deny'`|
|frameguard.domain|Domaine CORS associé à l'action `'allow-from'`| `'self'`|
|xss.enabled|Activation de la protection contre les failles XSS| `true`|
|xss.setOnOldIE|Force le header X-XSS-Protection sur les anciens IE| `true`|
|hpkp.enabled|Activation du Public Key Pinning: HPKP, HTTPS certificates can be forged, allowing man-in-the middle attacks|true|
|hpkp.maxAge|Durée de validation| `7776000000`|
|hpkp.sha256s|Liste des sha au format 256| `["AbCdEf123=","ZyXwVu456="]`|
|hpkp.includeSubdomains|Inclusion des sous domaines| `true`|
|hpkp.reportUri|Url de rapport| `null`|
|hpkp.reportOnly|Si valorisé à true, génération d'un rapport d'erreur uniquement| `false`|
|hsts.enabled|Activation du HTTP Strict Transport Security: hsts| `false`|
|hsts.maxAge|Durée de validation| `7776000000`|
|hsts.includeSubDomains|Inclusion des sous domaines| `true`|
|hsts.preload|Activation du preload dans le header HSTS| `false`|
|csrf.enabled|Activation du mode CSRF : Cross-Site Request Forgery| `true`|

Pour plus d'information sur CSP (Content Security Policy) : `https://www.w3.org/TR/CSP/`

Exemple de configuration : 

```json
   "cookie": {
     "httpOnly": true,
     "secure": false
   },
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
        "'unsafe-eval'",
        "${themeHost}"
      ],
      "styleSrc":[
        "'self'",
        "'unsafe-inline'",
        "${themeHost}"
      ],
      "fontSrc":[
        "'self'",
        "${themeHost}"
      ],
      "imgSrc":[
        "'self'",
        "${themeHost}"
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

## Anti Injections SQL, HTML

L'échappement des caractères spéciaux liés aux injections SQL, HTML est effectué nativement via React. 

En effet, l’escape est fait par React qui affiche le code en remplaçant le "code" par une valeur échappée et en affichant ce code sans l’exécuter.


