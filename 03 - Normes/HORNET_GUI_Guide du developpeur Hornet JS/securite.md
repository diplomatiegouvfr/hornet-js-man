# Les composants de sécurité

## Présentation
Ce chapitre détaille les solutions et bonnes pratiques du framework hornet pour prévenir les principales failles de sécurité web.

## Les attaques CSRF

### Définition

CSRF ou plus communément appelée "Cross Site Request Forgery" est une classe d'attaques propre aux applications web.


Elle reste l'une des moins médiatiques malgré les effets dévastateurs qu'elle peut présenter.
Issue d'un problème nommé "Confused deputy" [1] découvert par Norm Hardy en 1988,
l'attaque relative aux applications Web fut officiellement révélée en Juin 2001 par Peter Watkins sur la liste de diffusion "Bugtrack".

Elle fut surnommée "Cross Site Request Forgery" mais est plus communément appelé CSRF (prononcée Sea Surf) ou XSRF.

Cette technique exploite la confiance qu'une application montre à l'égard de ses clients.
Le but est de forcer le navigateur de la victime à envoyer une requête silencieuse à l'insu d'un internaute.
Au premier abord, cette attaque peut sembler difficile à mettre en place.
Détrompez-vous! Elle peut être menée par n'importe qui et d'une manière extrêmement simple.
Pour cela il suffit d'insérer un script dans une page web ou de le camoufler dans un e-mail.
En suivant le lien contrefait, le navigateur de la victime va exécuter une requête vers un site sur lequel la victime est authentifiée.

Méconnue par la plupart des RSSI, elle vise particulièrement les sites web dont les structures des requêtes utilisées sont prédictibles.

### Exemple d'attaque

Il suffit de poster un formulaire provenant d'une fausse page web avec tous les champs requis.

### La parade

La parade est heureusement assez simple. Il suffit de générer un token coté serveur et de l'envoyer au client.

Du coté client le token est renvoyé au moment du POST du formulaire. Enfin le serveur peut verifier si son token est le même.

Techniquement :

- Le middleware express 'Csrf' enrichie l'objet `request` afin de proposer une méthode générant un token et l'ajoutant dans la session.
- Le `router-view` utilise cette méthode pour retourner un nouveau token au client lors du rendu d'une page.
- Le navigateur récupère ce token au chargement de la page et l'envoi ensuite dans chaque reqûete Ajax effectuée (dans le header `x-csrf-token` ou dans le champ `x-csrf-token` du corps de la requête).
- Le middleware express 'Csrf' intercepte tous les POST, PUT, PATCH, DELETE
- Le middleware vérifie qu'un des token en session et le token dans l'entête de la requête sont bien identiques. Si c'est le cas l'ancien token est invalidé et un nouveau est généré puis envoyé dans le header `x-csrf-token` de la réponse. Le middleware délègue ensuite le traitement au middleware suivant. Par contre si le token est invalide, une erreur 417 est envoyée et aucun middleware n'est ensuite appelé.
- Le navigateur, en plus de récupérer le retour de la requête, remplace l'ancien token par le nouveau.

#### Inconvénient

Le fait de générer un nouveau token sur chaque requête POST, PUT, PATCH et DELETE et de retourner ce token au client à la fin des actions fait qu'il n'est pas possible d'effectuer plusieurs requêtes de ce type en parallèle.

En effet, par exemple si 2 requêtes POST sont effectuées en même temps, le navigateur va transmettre le même token dans chaque requête. La première reçue par le serveur va invalider le token et la seconde requête sera rejetée.

A noter que ceci a pour avantage de protéger contre la double validation de formulaire.

La **solution** est de chainer ces appels pour n'exécuter la deuxième requête que lorsque la première est terminée, et ainsi avoir reçu le nouveau token.

### Activer la securité csrf dans hornet

Pour activer la sécurité csrf il faut positionner `activated` à true dans le fichier de configuration.

 ```json
    ...
    "security": {
      "csrf": {
        "activated": true,
        "maxTokensPerSession": 10
      },
    },
    ...
```

La clé `maxTokensPerSession` permet de limiter le nombre de token possible par client, sachant que chaque onglet ouvert par un utilisateur génère un nouveau token, ceci limite finalement le nombre maximum d'onglets ouverts par un même utilisateur.

## Les protections à base de headers HTTP

### Définition

Plusieurs headers HTTP peuvent être ajoutés aux pages afin d'améliorer la sécurité au sein des navigateurs.
Hornet utilise la librairie **helmet** qui regroupe plusieurs de ces utilitaires.

### Les attaques

La description des attaques et leur parade sont expliquées sur le site de la librairie **helmet**: https://github.com/helmetjs/helmet

### Activer la sécurité des headers dans hornet

La configuration des différents 'middleware express' gérant ces headers se fait dans l'objet `security` de la configuration:

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
      "defaultSrc": [
        "'self'",
        "'unsafe-inline'",
        "'unsafe-eval'",
        "http://hornet-hemes
      ],
      "reportOnly": false,
      "setAllHeaders": false,
      "disableAndroid": false,
      "safari5": false
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
      "reportUri": null
    },
    "hsts": {
      "enabled": false,
      "maxAge": 10886400000,
      "includeSubdomains": true,
      "preload": false
    },
    "csrf": {
      "enabled": true,
      "maxTokensPerSession": 10
    }
  }
```

## La protection des paramètres en get: HTTP Parameter Pollution attacks

### Définition

L'ajout de paramètres à des requêtes GET (exemple: user?name=John&name=Johnny) peuvent provoquer des comportements inattendus.

La librairie 'hpp' propose un middleware express qui fiabilise ce comportement.

La page github de cette librairie présente le concept de l'attaque ainsi que la mise en œuvre pour y remédier: https://github.com/analog-nico/hpp

### Activer la securité des paramètres GET dans hornet

Pour activer cette sécurité il faut positionner 'hpp' à true dans le fichier de configuration.

 ```json
    ...
    "security": {
        "hpp": true
    },
    ...
```

## La protection les requetes http avec httpOnly et secure
 
Le cookie HTTPOnly est utilisé pour conserver les informations d’authentification afin de protéger ces dernières contre les attaques XSS.

Le cookie secure est utilisé pour prévenir la lecture des cookies par une personne non authorisée, la requête nécessite alors d'être en https.
 
### Activer la securité httpOnly et secure

Pour activer `httpOnly` il faut positionner 'httpOnly' à true dans le fichier de configuration.

Pour activer `secure` il faut positionner 'secure' à true dans le fichier de configuration.

 ```json
    ...
    "cookie": {
      "httpOnly": true,
      "secure": true
    },
    ...
```

## Anti Injections SQL, HTML

L'échappement des caractères spéciaux liés aux injections SQL, HTML est effectué nativement via React. En effet, l’escape est fait par React qui affiche le code en remplaçant le “code” par une valeur échappée et en affichant ce code sans l’exécuter.