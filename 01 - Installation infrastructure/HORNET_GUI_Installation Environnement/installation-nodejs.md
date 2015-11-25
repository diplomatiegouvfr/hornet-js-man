# Installation de NodeJS

L'installation de Node JS passe par : 

- l'installation de Node.js
- le déploiement de l'application
- la configuration du lanceur SystemD

## Installation de nodeJS

Récupérer le paquet nodejs sur le site officiel de nodejs.org dans sa version 4.2.2 linux x64.

Dézipper celui-ci dans le répertoire : 

```shell
"/usr/local"
```

Les répertoires de node doivent se confondre avec la configuration de l'arborescence Debian : 

 - bin
 - lib
 - ...

## Déploiement d'une application SystemD  

Les applications sont déployées dans le répertoire : 

```shell
/var/lib/nodejs/MON_APPLI  
```

La configuration de l'application est dans le sous répertoire de cette arborescence : 

```shell
./config/default.json 
```

En mode multi-instance (cluster), le répertoire de l'application est partagé par toutes les instances. 

## Mode de chargement des properties 

Par défaut, une application embarque un certain nombre de propriétés fonctionnelles mais également liées à une infrastructure. Lors du déploiement, les propriétés concernant la plateforme sont écrasées par celles stockés sur la système.

Ces données sont stockées dans le répertoire :

```shell
/etc/nodejs/MON_APPLI
```  

### Environnement

Un fichier d'environnement est présent : environnement

Le contenu : 

```shell
HORNET_CONFIG_DIR_APPLI=/var/lib/nodejs/MON_APPLI/config
HORNET_CONFIG_DIR_INFRA=/etc/nodejs/MON_APPLI
NODE_ENV=production
```

###production

Le fichier "production.json" contient des données communes aux instances d'application. 
Ex : le chemin vers le cdn

Fichier : **production.json**

Extrait : 

```json

{
  "contextPath": "MON_APPLI",
  "themeUrl": "[Protocol]://[host]:[port]/5.0.0/intranet",
  "cookie": {
    "httpOnly": true,
    "secure": false
  },
  "security": {
    "enabled": true,
    "csp": {
      "defaultSrc": [
        "'self'",
        "'unsafe-inline'",
        "'unsafe-eval'",
        "[Protocol]://[host]:[port]"
      ]
    }
  },
  "logClient": {
    "remote": false,
    "level": "TRACE",
    "appenders": [
      {
        "type": "BrowserConsole",
        "layout": {
          "type": "THIN"
        }
      }
    ]
  },
  "services": {
    "host": "[Protocol]://[host]:[port]/",
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

Fichier : **production-1.json**

Extrait : 

```json 
{
  "server":{
    "route": "js1",
    "port":8888
  },
  "log":{
    "levels": {
      "[all]": "INFO"
    },
    "appenders":[
      {
        "type":"file",
        "filename":"/var/log/nodejs/MON_APPLI/MON_APPLI-1.log",
        "layout":{
          "type":"pattern",
          "pattern":"%d{ISO8601}|%x{tid}|%x{user}|%p|%c|%m"
        }
      }
    ]
  }
}

```
  
##Note

Les fichiers production.json et production-instance.json peuvent être fusionnée dans le cas d'un serveur mono-instance.
  
## Configuration de SystemD

### Script SystemD

Créer le fichier permettant d'initier l'instance de l'application.
  
```
touch /etc/systemd/system/MON_APPLI.service
```

Mettre le contenu suivant :

```shell
[Service]
WorkingDirectory=/var/lib/nodejs/applitutoriel
ExecStartPre=/bin/echo "Demarrage de l'application MON_APPLI-INSTANCE"
ExecStart=/bin/bash -c "/usr/local/bin/node --harmony --stack-trace-limit=1000 --stack-size=1024 --trace_gc --trace_gc_verbose index.js >> /var/log/nodejs/MON_APPLI/log-gc.log 2>&1"
ExecStopPost=/bin/echo "Arret de l'application MON_APPLI-INSTANCE"
Restart=no
StandardOutput=inherit
StandardError=inherit
SyslogIdentifier=MON_APPLI-INSTANCE
User=nodejs
Group=nodejs
EnvironmentFile=/etc/nodejs/applitutoriel/environnement
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

Après la définition d'un service, celui-ci doit être rendu "enable".

```shell
systemctl enable MON_APPLI-INSTANCE.service
systemctl start MON_APPLI-INSTANCE.service
systemctl status MON_APPLI-INSTANCE.service
```

