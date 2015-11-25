# Cache de l'application

## Configuration
Le cache est par défaut disponible côté serveur et côté client.
Si il n'est pas définis dans le fichier `default.json`, il est actif et les objets sont conservés pendant 1 heure.
Il est configurable comme suit:

```javascript
  "commons": {
    [......]
    "cache" : {
      "active":true,
      "timetolive":60
    }
  }
```
Pour le désactiver, il faut le définir dans le fichier de configuration et positionner la variable `active` à `false`

## Utilisation du cache

### Cache sur les requêtes
    Voir la partie `cache` des `services rest`
