# Création du projet js à partir d'un template yeoman

Installer Yeoman :

```shell
npm install -g yo
```

Installer le template Yeoman pour un projet hornet-js :

```shell
npm install -g generator-hornet-js
```

Créer un répertoire pour l'application, s'y positionner et lancer la génération d'un nouveau projet hornet-js :

```shell
mkdir applitutoriel-js
cd applitutoriel-js
```

```shell
yo hornet-js
```

| Information                       | Valeur par défaut | Valeur à saisir       |
|---------------------------------- | ----------------- | --------------------- |
| Nom de votre projet               | &nbsp;            | applitutoriel-js      |
| Version de votre projet           | 1.0.0             | 1.0.0                 |
| Description de votre projet       | &nbsp;            | application tutoriel  |
| Version du framework (hornet-js)  | &nbsp;            | 5.x.x                 |
| Thême de l'application            | &nbsp;            | hornet-themes-intranet|
| Version du thême de l'application | &nbsp;            | 5.x.x                 |
| Host de la partie service         | &nbsp;            | http://localhost:8080 |
| ContextPath de la partie service  | &nbsp;            | applitutoriel         |
| Mode full spa                     | &nbsp;            | false                 |

## Démarrer l'application

Positionnez vous dans le répertoire de l'application Hornet générée précédemment et lancez l'installation :

```shell
hb install
```

Une fois l'installation terminée, compilez les sources :

```shell
hb compile
```

L'application est prête à être démarrée :

```shell
hb w -i
```