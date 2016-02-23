# Les composants 'server-log' et 'server-log-configurator' du module hornet-js-core

## Présentation

Ces composants sont responsables de l'initialisation et de la configuration de la log du serveur NodeJS.

Le module `server-log` configure et initialise le système log4js. Il fournit également une fonction buildLogger qui permet d'instancier un logger log4js.

Le module `server-log-configurator` va lire les informations du fichier de configuration (log4js.json). Il envoie ensuite cette configuration à server-log afin d'initialiser log4js, puis il redéfinit la fonction buildLogger de la classe Logger (elle est remplacée par la fonction fournie par server-log).


## Utilisation

Il suffit d'un require du composant `server-log-configurator` dans le code serveur de l'application pour déclencher l'initialisation. Exemple dans applitutoriel (`src\index.ts`) :

```javascript
...
// auto configuration des logs server
require("hornet-js-core/src/log/server-log-configurator");
...
```

## Fichier de configuration log4js.json

Le fichier log4js.json regroupe la configuration de la log serveur.

La version par défaut ne contient qu'un appender de type console, mais il est possible d'en rajouter autant que nécessaire.

Pour chaque appender, le format de la log peut être personnalisé grâce au layout. 

### Appender disponibles

Log4js embarque plusieurs appenders, les plus importants sont : `file` et `dateFile`.

Ces appenders écrivent dans les fichiers de manière asynchrone et sont donc risqués à utiliser car si le process nodejs crashe alors il y a de gros risques que les dernières lignes de logs n'aient pas eu le temps d'être écrites.

Pour sécuriser l'écriture des logs, il existe dans log4js un appender "fileSync" qui écrit de manière synchrone.
Hors il n'existe pas d'appender synchrone faisant de la rotation basée sur date.

C'est pourquoi un appender `hornet-js-utils/src/dateFileSyncAppender` a été implémenté dans hornet-js-utils, il se paramètre de la même façon que l'appender "dateFile"

Il convient donc d'utiliser uniquement les deux appender suivants dans la clé de configuration "type" :

- `fileSync`
- `hornet-js-utils/src/dateFileSyncAppender`

### Configurer le layout

Au niveau d'un appender, le format d'une ligne de log est défini via la clé de configuration layout.pattern.

Exemple de configuration dans `log4js.json` :

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
ou avec rotation de date :

```json
"appenders":[
      {
         "type":"hornet-js-utils/src/dateFileSyncAppender",
         "pattern":".yyyy-MM-dd",
         "filename":"/var/log/monLog.log",
         "layout":{
            "type":"pattern",
            "pattern":"%d{ISO8601}|%x{tid}|%x{user}|%p|%c|%m"
         }
      }
   ]
```

Pour composer le pattern, des variables prédéfinies sont disponibles : la date (%d), le niveau de log (%p), etc...

Hornet fournit également ses propres variables, à utiliser avec la syntaxe `%x{token}`. Les tokens fournis de base : 

* `pid` (id du procesus courant)
* `tid` (id du thread node courant)
* `user` (utilisateur connecté)
* `fn` (fonction appellante).

### Ajouter un token

Au besoin, l'application peut définir des tokens supplémentaires. Pour cela, la partie serveur doit être modifiée comme suit (fichier `src\index.ts`) :

```javascript
...
// auto configuration des logs server
require("hornet-js-core/src/log/server-log-configurator");

// ajout token log4js
import ServerLog = require("hornet-js-core/src/log/server-log");
ServerLog.addLayoutTokens({
    "platform": function () {
        return process.platform;
    },
    "arch": function () {
        return process.arch;
    }
});
...
```
