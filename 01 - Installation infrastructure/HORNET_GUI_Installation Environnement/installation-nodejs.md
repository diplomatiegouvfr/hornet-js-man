# Installation de Node.js

L'installation de Node.js passe par : 

- la décompression du livrable officiel Node.js
- le déploiement de l'application
- la configuration du lanceur SystemD

## Installation de Node.js

Récupérer le paquet nodejs sur le site officiel de nodejs.org dans sa version `4.3.X` `linux x64`.

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
  "themeUrl": "${themeHost}/5.X.X/default",
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
    "appenders": [
      {
        "type": "BrowserConsole",
        "layout": {
          "type": "THIN"
        }
      }
    ]
  },
  "defaultServices": {
    "host": "[Protocol]://[host]:[port]",
    "name": "MON_APPLI_SERVICE"
  },
  "mock": {
    "enabled": false
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
   "levels":{
      "[all]":"INFO"
   },
   "appenders":[
      {
         "type":"hornet-js-utils/src/dateFileSyncAppender",
         "pattern":".yyyy-MM-dd",
         "filename":"/var/log/nodejs/MON_APPLI/MON_APPLI-1.log",
         "layout":{
            "type":"pattern",
            "pattern":"%d{ISO8601}|%x{tid}|%x{user}|%p|%c|%m"
         }
      }
   ]
}
```
  
Les fichiers production.json et production-instance.json peuvent être fusionnée dans le cas d'un serveur mono-instance.
  
## Configuration de SystemD

### Script SystemD

Créer le fichier permettant d'initier l'instance de l'application.
  
```shell
$ touch /etc/systemd/system/MON_APPLI.service
```

Mettre le contenu suivant :

```shell
[Service]
WorkingDirectory=/var/lib/nodejs/MON_APPLI
ExecStartPre=/bin/echo "Demarrage de l'application MON_APPLI-INSTANCE"
ExecStart=/bin/bash -c "/usr/local/bin/node --harmony --stack-size=1024 --trace_gc --trace_gc_verbose index.js >> /var/log/nodejs/MON_APPLI/log-gc-INSTANCE.log 2>&1"
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
$ touch /etc/systemd/system/MON_APPLI-1.service
$ touch /etc/systemd/system/MON_APPLI-2.service
$ touch /etc/systemd/system/MON_APPLI-3.service
```

Modifier la variable "NODE_APP_INSTANCE" dans chaque script de service.

### Initialisation des services et démarrage

Après la définition d'un service, celui-ci doit être rendu `enable`.

```shell
$ systemctl enable MON_APPLI-INSTANCE.service
$ systemctl start MON_APPLI-INSTANCE.service
$ systemctl status MON_APPLI-INSTANCE.service
```

## Monitoring


### Pré-requis

Vérifier l'accès à un repository npm. 

Pour l'accès à un repository privé, créer le fichier `.npmrc` dans le home du user courant.

Exemple d'accès vers un repository privé : 

```shell
registry = [PROTOCOLE]://[HOST][PORT]/artifactory/api/npm/repository-npm
_auth = aGV1cnRlbWF0dGVzOkFQQlpFWkFRVTZrZ2huc255a1loTUQyV25VSA==
always-auth = true
email = sebastien.heurtematte@diplomatie.gouv.fr
```

### Installation

Installer le module de monitoring dans le répertoire : `/usr/local/share/hornet-global-modules`

Créer le répertoire `hornet-global-modules` :

```shell
$ mkdir /usr/local/share/hornet-global-modules
```

Installer le module : 

```shell
$ cd /usr/local/share/hornet-global-modules
$ npm install hornet-js-gc-monitor
$ mv node_modules/hornet-js-gc-monitor .
$ rm -Rf node_modules
```

Le composant a besoin de se compiler suivant la plateforme. 
Dans le cas, où l'accès internet n'est pas possible, définir un repo local telque :

```shell
$ cd /usr/local/share/hornet-global-modules
$ npm install hornet-js-gc-monitor --disturl=[PROTOCOLE]://[HOST][PORT]/artifactory/repository/nodejs/
$ mv node_modules/hornet-js-gc-monitor .
$ rm -Rf node_modules
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
  sourceUrl: 'https://nodejs.org/download/release/v4.3.0/node-v4.3.0.tar.gz',
  headersUrl: 'https://nodejs.org/download/release/v4.3.0/node-v4.3.0-headers.tar.gz' }
```

Pour une utilisation en environnement cloisonné, l'option `dist-url` permet de récupérer le header nodejs préalablement installé sur un repository privé. 

```shell
npm install node-gyp -g
node-gyp install -dist-url="[PROTOCOLE]://[HOST][PORT]/artifactory/repository/nodejs/"
```