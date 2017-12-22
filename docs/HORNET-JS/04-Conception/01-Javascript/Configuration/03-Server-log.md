# Les composants 'server-log' et 'server-log-configurator'

## Présentation

Ces composants sont responsables de l'initialisation et de la configuration de la log du serveur NodeJS.

Le module `server-log` configure et initialise le système log4js. Il fournit également une fonction buildLogger qui permet d'instancier un logger log4js.

Le module `server-log-configurator` va lire les informations du fichier de configuration (log4js.json). Il envoie ensuite cette configuration à server-log afin d'initialiser log4js, puis il redéfinit la fonction buildLogger de la classe Logger (elle est remplacée par la fonction fournie par server-log).


## Utilisation

Il suffit d'un import du composant `server-log-configurator` dans le code serveur de l'application pour déclencher l'initialisation. Exemple dans applitutoriel (`src\index.ts`) :

```javascript
...
// auto configuration des logs server
import { ServerLogConfigurator } from "hornet-js-core/src/log/server-log-configurator";
...
```

## Fichier de configuration log4js.json

Le fichier log4js.json regroupe la configuration de la log serveur.

La version par défaut ne contient qu'un appender de type console, mais il est possible d'en rajouter autant que nécessaire.

Pour chaque appender, le format de la log peut être personnalisé grâce au layout. 

### Appender disponibles

Log4js embarque plusieurs appenders, les plus importants sont : `file` et `dateFile`.


### Configurer le layout

Au niveau d'un appender, le format d'une ligne de log est défini via la clé de configuration layout.pattern.

Exemple de configuration dans `log4js.json` :

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
  },
  "categories": {
     "default": { "appenders": ["console"], "level": "INFO" }
  }
}
```
ou avec rotation de date :

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
   },
   "categories": {
      "default": { "appenders": ["dateFile"], "level": "INFO" }
   }
}
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
