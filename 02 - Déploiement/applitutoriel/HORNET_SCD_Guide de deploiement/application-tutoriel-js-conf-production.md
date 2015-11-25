### Fichier de configuration : production.json

Le fichier "production.json" contient des données communes aux instances d'application. 
Ex : le chemin vers le cdn

C'est ce fichier qui doit être mis à jour en priorité pour une installation.

```json
{
  "contextPath": "applitutoriel",
  "themeHost": "http://10.104.19.108",
  "themeUrl": "${themeHost}/hornet/5.0.0/intranet",
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
        "${themeHost}"
      ],
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
      ],
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
  "services": {
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

### Fichier de configuration par instance : production-1.json

Ce fichier n'est pas obligatoire dans le cas d'un mode mono-instance. Dans ce cas précis il faut reporter toutes les modification sur le production.json. 

Dans le cas d'un mode cluster, il faut créer autant de fichier qu'il y a d'instance. 

Ceci s'explique par la configuration du port d'exécution mais également le fichier de log de l'instance.

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
        "filename":"/var/log/nodejs/applitutoriel/applitutoriel-1.log",
        "layout":{
          "type":"pattern",
          "pattern":"%d{ISO8601}|%x{tid}|%x{user}|%p|%c|%m"
        }
      }
    ]
  }
}


```
