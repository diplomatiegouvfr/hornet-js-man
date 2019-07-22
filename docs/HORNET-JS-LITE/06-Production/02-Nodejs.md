# Installation de Node.js

L'installation de Node.js passe par : 

- la décompression du livrable officiel Node.js
- le déploiement de l'application
- la configuration du lanceur SystemD

## Installation de Node.js

Récupérer le paquet nodejs sur le site officiel de nodejs.org dans sa version `8.9.X` `linux x64`.

Dézipper celui-ci dans le répertoire : `/usr/local`

Les répertoires de node doivent se confondre avec la configuration de l'arborescence Debian : 

 - bin
 - lib
 - ...

## Droit sur les répertoires

Créer le user `nodejs`

```shell
$ useradd -mrU -c "Utilisateur applicatif nodejs" -s /usr/sbin/nologin -b /var/lib nodejs
```

Les droits sur les répertoires suivants doivent être `755` avec comme propriétaire/groupe :`nodejs/nodejs`.

 - /var/lib/nodejs 
 - /var/log/nodejs
 - /etc/nodejs

## Déploiement d'une application SystemD  

Les applications sont déployées dans le répertoire : `/var/lib/nodejs/MON_APPLI`

La configuration de l'application est dans le sous répertoire de cette arborescence : `./config/default.json `

En mode multi-instance (cluster), le répertoire de l'application est partagé par toutes les instances. 

## Mode de chargement des properties 

Par défaut, une application embarque un certain nombre de propriétés fonctionnelles mais également liées à une infrastructure. Lors du déploiement, les propriétés concernant la plateforme sont écrasées par celles stockés sur la système.

Ces données sont stockées dans le répertoire : `/etc/nodejs/MON_APPLI`  

### Environnement

Un fichier d'environnement est présent : `environnement`

Le contenu : 

```shell
HORNET_CONFIG_DIR_APPLI=/var/lib/nodejs/MON_APPLI/config
HORNET_CONFIG_DIR_INFRA=/etc/nodejs/MON_APPLI
HORNET_GLOBAL_MODULE_DIR=/usr/local/share/hornet-global-modules
NODE_ENV=production
```

La variable `NODE_ENV` est positionné avec la valeur `production` et ne doit pas être modifiée. 
Il s'agit d'une variable propre à node.js, par défaut `development`.

### Production

Le fichier `production.json` contient des données communes aux instances d'application. 
Ex : le chemin vers le cdn

Exemple : 

```json
{
  "contextPath": "applitutoriel",
  "themeHost": "[Protocol]://[host]:[port]",
  "themeUrl": "${themeHost}/5.X.X/intranet",
  "server": {
    "keepAlive": true,
    "keepAliveTimeout": 15000,
    "maxConnections": 100,
    "timeout": 300000,
    "uploadFileSize": 1000000,
    "sessionTimeout": 1800000,
    "stackTraceLimit": 100
  },
  "keystore": {
    // Autorités de certification
    "CAs": [
      "/path/to/igca.pem"
    ],

    // Cas d'authentification SSL
    "CERTs" : [
      "/path/to/client/certificate/in/pem/format"
    ],
    "KEYs": [
      {
        "file": "/path/to/client/private/key/in/pem/format",
        "passphrase": "abcdefgh"
      }
    ],
    // Cas d'un conteneur PKCS12
    "PKCS12": {
      "file": "/path/to/pkcs12",
      "passphrase": "abcdefgh"
    }
  },
  "cookie": {
    "httpOnly": true,
    "secure": false
  },
  "security": {
    "enabled": true,
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
      ]
    }
  },
  "logClient": {
    "remote": false,
    "level": "INFO",
    "appenders": {
        "BrowserConsole" : {
             "type": "BrowserConsole",
             "layout": {
               "type": "THIN"
             }
         }
    }
  },
  "defaultServices": {
    "host": "[Protocol]://[host]:[port]",
    "name": "MON_APPLI_SERVICE"
  },
  "mock": {
    "enabled": false,
    "servicePage": {
      "enabled": false
    },
    "serviceData": {
      "enabled": false
    }
  },
  "fullSpa": {
    "enabled": false
  }
}

```

### Production-instance

Dans le cas d'un mode cluster, il est possible de définir des propriétés par instance.
Ex : des répertoires de log différent, un numéro de port différent

Fichier : `production-1.json`

Extrait : 

```json 
{
  "server":{
    "route": "js1",
    "port":8888,
    "portMonitor": 9999
  }
}
```

### log4js-instance

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
    "dateFile": {
      "type": "dateFile",
      "pattern": ".yyyy-MM-dd",
      "filename": "log/{#INSTANCE_NAME}.log",
      "layout": {
        "type": "pattern",
        "pattern": "%d{ISO8601}|%x{tid}|%x{user}|%p|%c|%x{fn}|%m"
      },
      "compress": true,
      "keepFileExt": true
    }
  },
  "categories": {
    "default": { "appenders": ["console" , "dateFile", "level": "INFO" }
  }
}
```
  
Les fichiers production.json et production-instance.json peuvent être fusionnés dans le cas d'un serveur mono-instance.

### Logs attendues
Le chargement de la configuration server se fait en deux étapes. 
Dans un premier temps, la configuration présente dans le dossier donné par la variable HORNET_CONFIG_DIR_APPLI est chargée. Ce dossier ne contenant pas les fichiers production.json et production-1.json, les lignes suivantes apparaîtront alors dans les logs de démarrage (ce qui est normal) :

```shell
NO_TID|NO_USER|ERROR|hornet-js.console|Logger.(anonymous function)|WARNING: NODE_ENV value of 'production' did not match any deployment config file names.
NO_TID|NO_USER|ERROR|hornet-js.console|Logger.(anonymous function)|WARNING: See https://github.com/lorenwest/node-config/wiki/Strict-Mode
NO_TID|NO_USER|ERROR|hornet-js.console|Logger.(anonymous function)|WARNING: NODE_APP_INSTANCE value of '1' did not match any instance config file names.
NO_TID|NO_USER|ERROR|hornet-js.console|Logger.(anonymous function)|WARNING: See https://github.com/lorenwest/node-config/wiki/Strict-Mode
```
Ensuite est chargée la configuration présente dans le dossier donné par la variable HORNET_CONFIG_DIR_INFRA qui contient les fichiers production.json et production-1.json.
Ces deux configurations sont par la suite mergées ce qui constitue la configuration des instances.

La ligne suivante doit apparaître dans les logs une fois que le merge des deux configurations est effectué :

```
NO_TID|NO_USER|INFO|hornet-js-utils.config-lib|Logger.(anonymous function)|Configuration mergée :  ...

```
  
## Configuration de SystemD

### Script SystemD

Créer le fichier permettant d'initier l'instance de l'application.
  
```shell
touch /etc/systemd/system/MON_APPLI.service
```

Mettre le contenu suivant :

```shell
[Service]
WorkingDirectory=/var/lib/nodejs/MON_APPLI
ExecStartPre=/bin/echo "Demarrage de l'application MON_APPLI-INSTANCE"
ExecStartPre=/bin/bash -c "/bin/tar -zcvf /var/log/nodejs/MON_APPLI-INSTANCE/log-gc-INSTANCE-$$(date +%%Y%%m%%d%%H%%M%%S).tar.gz /var/log/nodejs/MON_APPLI-INSTANCE/log-gc-INSTANCE.log --remove-files --ignore-failed-read"
ExecStart=/bin/bash -c "/usr/local/bin/node --harmony --stack-size=1024 --trace_gc --trace_gc_verbose index.js > /var/log/nodejs/MON_APPLI/log-gc-INSTANCE.log 2>&1"
ExecStopPost=/bin/echo "Arret de l'application MON_APPLI-INSTANCE"
Restart=no
StandardOutput=inherit
StandardError=inherit
SyslogIdentifier=MON_APPLI-INSTANCE
User=nodejs
Group=nodejs
EnvironmentFile=/etc/nodejs/MON_APPLI/environnement
Environment='NODE_APP_INSTANCE=1'
 
[Install]
WantedBy=multi-user.target 
```

### Multi-instance

Pour chaque instance en mode cluster, créer un fichier différent avec le numéro d'instance.

```shell
touch /etc/systemd/system/MON_APPLI-1.service
touch /etc/systemd/system/MON_APPLI-2.service
touch /etc/systemd/system/MON_APPLI-3.service
```

Modifier la variable "NODE_APP_INSTANCE" dans chaque script de service.

### Initialisation des services et démarrage

Après la définition d'un service, celui-ci doit être rendu `enable`.

```shell
systemctl enable MON_APPLI-INSTANCE.service
systemctl start MON_APPLI-INSTANCE.service
systemctl status MON_APPLI-INSTANCE.service
```

## Monitoring


### Pré-requis

Vérifier l'accès à un repository npm. 

Pour l'accès à un repository privé, créer le fichier `.npmrc` dans le home du user courant.

Exemple d'accès vers un repository privé : 

```shell
registry = [PROTOCOLE]://[HOST][PORT]/artifactory/api/npm/repository-npm
_auth = XXXXXX
always-auth = true
email = dev@diplomatie.gouv.fr
phantomjs_cdnurl = [PROTOCOLE]://[HOST][PORT]/[URL_ARTIFACTORY]/repository-npm-tiers/phantomjs
disturl = [PROTOCOLE]://[HOST][PORT]/[URL_ARTIFACTORY]/repository-npm-tiers
```

### Installation

Installer le module de monitoring dans le répertoire : `/usr/local/share/hornet-global-modules`

Créer le répertoire `hornet-global-modules` :

```shell
mkdir /usr/local/share/hornet-global-modules
```

Installer le module : 

```shell
cd /usr/local/share/hornet-global-modules
npm install hornet-js-gc-monitor
mv node_modules/hornet-js-gc-monitor .
rm -Rf node_modules
```

Le composant a besoin de se compiler suivant la plateforme. 
Dans le cas, où l'accès internet n'est pas possible, définir un repo local telque :

```shell
cd /usr/local/share/hornet-global-modules
npm install hornet-js-gc-monitor --disturl=[PROTOCOLE]://[HOST][PORT]/artifactory/repository/nodejs/
mv node_modules/hornet-js-gc-monitor .
rm -Rf node_modules
```

### Autre possibilité d'installation des headers nodejs

Afin de compiler le module hornet-js-gc-monitor, il est nécessaire d'installer les headers nodejs. 

```shell
npm install node-gyp -g
node-gyp install
```

Par défaut, node-gyp récupère les headers spécifiés dans nodejs.

L'url par défaut est récupérable via la commande : 

```shell
node -p process.release
```

Résultat :

```shell
{ name: 'node',
  lts: 'Argon',
  sourceUrl: 'https://nodejs.org/download/release/v8.9.X/node-v8.9.X.tar.gz',
  headersUrl: 'https://nodejs.org/download/release/v8.9.X/node-v8.9.X-headers.tar.gz' }
```

Pour une utilisation en environnement cloisonné, l'option `dist-url` permet de récupérer le header nodejs préalablement installé sur un repository privé. 

```shell
npm install node-gyp -g
node-gyp install -dist-url="[PROTOCOLE]://[HOST][PORT]/artifactory/repository/nodejs/"
```