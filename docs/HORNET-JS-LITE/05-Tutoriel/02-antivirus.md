# Antivirus

## Objectif

Pouvoir scanner le contenu d'un fichier par l'antivirus. L'antivirus préconisé est clamav.

## Principe

Interragir avec un serveur portant le service clamav.
Pour cela, il faut configurer l'adresse de celui-ci dans le fichier de configuration de l'application.

## Configuration

### Service distant

Il faut ajouter la configuration dans votre l'application.

```json
  "antivirus" : {
    "host" : "localhost",
    "port" : 3310,
    "timeout" : 60000
  }
```

### Activation de l'antivirus dans le composant d'upload Hornet

```json
  "server":{
    "uploadAntivirus": true
  }
```

## Exemple d'utilisation en direct

```javascript

import { ClientAntivirusConnexion } from "hornet-js-core/security/client-antivirus-connexion";

staff(stream : FileStream){
    new ClientAntivirusConnexion().scan(stream)
        .then((data : Buffer) => {
            ...
        })
        .catch((err)=>{
            ...
        });
}

```