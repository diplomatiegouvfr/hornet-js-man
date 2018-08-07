# Utilisation de Plop

Nous mettons à disposition des générateurs Plop pour aider les projets sur certaines tâches. Ces générateurs par défaut pourront être adaptés à chacune des applications, suivant les besoins.

## Installer Plop

Il faut avoir créé un projet Hornet JS à l'aide du [template Yeoman](/hornetshowroom/composant/page/hornet-js/tutoriel/javascript/creer-un-projet).

Sous le projet créé : 

```shell

npm install

```

## Lancer Plop

Sous le projet : 

```shell

npm run plop

```

## Les générateurs

De base, nous proposons des générateurs pour ajouter des routes lazy. Puis dans ces routes lazy, des générateurs pour ajouter des routes de type `page` et `data`.

Le générateur est amené à évoluer pour chaque projet, notamment pour configurer les rôles propres au projet, utiliser des chemins particuliers ou ajouter d'autres générateurs.

