# Cookie manager

La classe CookieManager gère la création, récupération et suppression de cookie.

## Configuration

La durée de vie par défaut des cookies de l'application est 1 heure, elle peut être modifiée globalement avec l'attribut *defaultDuration*.

Exemple : 

```javascript

"cookie" : {
 "defaultDuration": 3600 // in second
}

```

## Utilisation du cache

La classe met à disposition 3 méthodes statiques

```javascript

getCookie(req:IncomingMessage, name, options?: CookieManagerOption)
setCookie(res:ServerResponse, name:string, value:any, options?:CookieManagerOption)
removeCookie(res, name)

```

avec la signature de l'objet de configuration :

```javascript

interface CookieManagerOption {
    secret?: string;
    route?: string;
    maxAge?: number;
    path?: string;
}

```

| attribut | Description |
|----------|------------|
|secret| Clé de signature |
|route| Route pour la HD |
|maxAge| Durée de vie en seconde |
|path| Chemin associé au cookie |
