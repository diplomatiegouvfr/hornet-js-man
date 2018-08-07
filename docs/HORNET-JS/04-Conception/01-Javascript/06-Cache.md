# Cache de l'application

## Configuration

Le cache est par défaut disponible côté serveur et côté client. Par défaut, il n'est pas activé.

Configuration possible du cache.

```javascript
"request": {
    "cache" : {
      "enabled":true,
      "timetolive":60 // en secondes
    }
}
```

La configuration permet d'avoir une gestion de cache différente côté serveur et côté client, voici l'exemple de configuration à mettre en place dans ce cas :

```javascript
"request": {
    "cache": {
      "client": {
        "enabled": false,
        "timetolive": 60
      },
      "server": {
        "enabled": false,
        "timetolive": 120
      }
    }
}
```

## Utilisation du cache

### Sur les requêtes de services

Voir la partie `cache` des `services rest`
