# Installation de Clamav

Clamav est un logiciel antivirus. Il est généralement utilisé avec les servers de courriels pour filtrer ces derniers. Il est également utilisé pour les analyses de pièces jointes lors de saisie de formulaire.


## Installation du service clamav

Récupération et installation des paquets clamav et clamav-daemon

```shell

    apt-get update ; apt-get install clamav; apt-get install clamav-daemon;

```

## Commandes du service:

### Arrêt

```shell

    systemctl stop clamav-daemon.service

```
### Démarrage

```shell

    systemctl start clamav-daemon.service

```

### Redémarrage

```shell

    systemctl restart clamav-daemon.service

```

### Status

```shell

    systemctl status clamav-daemon.service

```

## Configuration

```shell

    nano /etc/clamav/clamd.conf

```

Ajouter la ligne en bas de fichier afin de définir un port d'écoute TCP:

```

    TCPSocket 3310

```

Activer les log en modifiant les valeurs suivantes:

```

    LogRotate true
    LogClean true
    LogVerbose true

```
