### Fichier de configuration : production.json

Le fichier `/etc/nodejs/applitutoriel/production.json` contient des données communes aux instances d'application. Ex : le chemin vers le cdn

C'est ce fichier qui doit être mis à jour en priorité pour une installation.

```json
{
  "contextPath": "applitutoriel",
  "themeHost": "[Protocol]://[host]:[port]",
  "themeUrl": "${themeHost}/hornet/5.0.X/default",
  "server": {
    "keepAlive": true,
    "keepAliveTimeout": 15000,
    "maxConnections": 100,
    "timeout": 300000,
    "uploadFileSize": 1000000,
    "sessionTimeout": 1800000,
    "stackTraceLimit": 100
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
        "'unsafe-inline'"
        "${themeHost}"
      ],
      "fontSrc":[
        "'self'",
        "[Protocol]://[host]:[port]"
        "${themeHost}"
      ],
      "imgSrc":[
        "'self'",
        "[Protocol]://[host]:[port]"
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
    "host": "http://10.104.19.114:8080/",
    "name": "applitutoriel-service"
  },
  "mock": {
    "enabled": false
  },
  "fullSpa": {
    "enabled": false
  }
}
```

### Fichier de configuration par instance : production-INSTANCE.json

Ce fichier n'est pas obligatoire dans le cas d'un mode mono-instance. Dans ce cas précis, il faut reporter toutes les modification sur le production.json. 

Dans le cas d'un mode cluster, il faut créer autant de fichier qu'il y a d'instance. 

ex : `/etc/nodejs/applitutoriel/production-1.json`

Ceci s'explique par la configuration du port d'exécution mais également le fichier de log de l'instance.

```json 
{
  "server":{
    "route": "js1",
    "port":8888,
    "portMonitor": 9999,
  }
}
```

### Fichier de configuration des logs par instance : log4js-INSTANCE.json

Ce fichier est obligatoire pour que l'application démarre. 

ex : `/etc/nodejs/applitutoriel/log4js-1.json`

```json 
{
   "levels":{
      "[all]":"INFO"
   },
   "appenders":[
      {
         "type":"hornet-js-utils/src/dateFileSyncAppender",
         "pattern":".yyyy-MM-dd",
         "filename":"/var/log/nodejs/applitutoriel/applitutoriel-1.log",
         "layout":{
            "type":"pattern",
            "pattern":"%d{ISO8601}|%x{tid}|%x{user}|%p|%c|%m"
         }
      }
   ]
}
```
