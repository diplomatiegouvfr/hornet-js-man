# generator-hornet-js

## Description

Le module Node « generator-hornet-js » contient les éléments de création d'une application Hornet JS. Il est utilisable avec Yeoman.

## Pré-requis

- NodeJS 4.X
- hornet-js-builder 1.X installé en global:

```shell
    $ npm install -g hornet-js-builder
```

## Utilisation

### Yeoman

[Yeoman](http://yeoman.io/) est un outil permettant de construire des outils de génération de projets. Celui-ci s'appuie sur plusieurs composants dont Yo qui s'occupe plus spécifiquement de l'aspect génération.

Hornet JS propose un générateur d'application basé sur Yo.

Pour installer Yeoman, exécuter la ligne de commande suivante :

```shell
$ npm install -g yo
```

### Installation du générateur

```shell
$ npm install -g generator-hornet-js
```

### Initialisation d'un projet

Tout d'abord, il vous faut créer un dossier pour accueillir votre nouveau projet : 

```shell
$ mkdir nom_de_l_application
$ cd nom_de_l_application
```

Dans le répertoire destiné à accueillir le code de l'application, exécuter la ligne de commande suivante pour débuter la génération d'un nouveau projet basé sur `hornet.js` :

```shell
$ yo hornet-js
```

Le générateur va vous demander plusieurs informations nécessaires à l'initialisation de votre projet Hornet JS :

* ` Nom de votre projet: (nom_de_l_application) ` : par défaut, le nom du répertoire est suggéré comme nom technique du projet
* ` Titre de l'application ` : le titre de l'application apparaît dans le bandeau de tête et le titre des pages de l'application.
* ` Description de votre projet ` : un texte court présentant succinctement le projet.
* ` Version de votre projet:` `(1.0.0)` : version du projet (`1.0.0` par défaut).
* ` Mode applicatif:` `(isomorphic)` : mode applicatif parmi 2 modes: isomorphic ou fullSPA:.
* ` Version du framework (hornet-js):` `(5.0.X)` : version du framework hornet-js (`5.0.X` par défaut identique à generator-hornet-js).
* ` Url du thème: (default) ` : l'url du thème à utiliser pour l'application (`[Protocol]://[host]:[port]/5.0.X/default` par défaut)
* ` URL de base des services de l'application: ` : URL où sont accessibles les services (partie Java Tomcat) de votre application.
* ` Nom/contexte des services de l'application ` : nom de l'application fournissant les services (partie Java Tomcat) de votre application.
* ` Version des services de l'application:` `(1.0.0)` : version de l'application fournissant les services (partie Java Tomcat) de votre application. Par défaut le numéro de version est le même que celui donné au début de cette procédure.

Le processus d'initialisation vous indique ensuite les fichiers créés :

```
   create package.json
   create builder.js
   create tsconfig.json
   create index.ts
   create config\default.json
   create src\resources\navigation.json
   create config\log4js.json
   create static\img\error.gif
   create static\img\key-point_bl.gif
   create static\img\key-point_br.gif
   create static\img\key-point_tl.gif
   create static\img\key-point_tr.gif
   create static\img\logoHornet.png
   create src\actions\cnt\gen-cnt-actions.ts
   create src\dispatcher\app-dispatcher.ts
   create src\i18n\app-i18n-loader.ts
   create src\resources\messages.json
   create src\resources\messages-fr-FR.json
   create src\routes\routes.ts
   create src\routes\cnt\gen-cnt-routes.ts
   create src\services\cnt\gen-cnt-api.ts
   create src\services\gen\gen-cnx-api.ts
   create src\stores\cnt\gen-cnt-store.ts
   create src\utils\roles.ts
   create src\views\cnt\gen-cnt-form.ts
   create src\views\cnt\gen-cnt-page.tsx
   create src\views\layouts\hornet-layout.jsx
   create src\views\nav\nav-pap-page.jsx
   create src\views\gen\gen-acb-page.jsx
   create src\views\gen\gen-aid-page.jsx
   create src\views\gen\gen-err-page.jsx
   create src\views\gen\gen-hea-cmp.jsx
   create src\views\gen\gen-acs-cmp.jsx
   create src\views\gen\gen-hom-page.tsx
   create src\views\gen\gen-ddc-page.jsx
   create src\views\gen\gen-foo-cmp.jsx
   create src\views\gen\gen-cnt-cmp.jsx
   create src\views\gen\gen-cnx-page.jsx
   create src\views\gen\theme\gen-ter-cmp.jsx
   create static\css\theme.css
   create static\css\auth.css
   create src\views\hornet-app.jsx
   create src\client.ts
   create src\server.ts
   create src\mock\routes.ts
   create src\mock\data\example.json
   create .npmignore
   create README.md
   create LICENSE.md
```

### Options avancées

#### Paramètre nom du projet

Il est possible de fournir directement le nom du projet à la ligne de commande :

```shell
$ yo hornet-js nom_de_l_application
```
Dans ce cas, la première question n'est pas posée et la valeur passée en paramètre est utilisée.

### Actions post-génération

#### Mode isomorphic

##### Récupération des dépendances

Une fois le projet initialisé, vous devez lancer l'installation des dépendances Node.js avec la commande suivante :

```shell
$ hb install
```

##### Configuration

Si vous souhaitez modifier la configuration de l'application, vous pouvez éditer le fichier suivant :

* `config\default.json`

##### Démarrage de l'application

Pour exécuter votre nouvelle application, utilisez la commande suivante :

```shell
$ hb w
```

Puis, utiliser un navigateur web pour y accéder par l'url [http://localhost:8888/nom_de_l_application](http://localhost:8888/nom_de_l_application).


#### Mode fullSPA

##### Récupération des dépendances

Une fois le projet initialisé, vous devez lancer l'installation des dépendances Node.js avec la commande suivante :

```shell
$ hb install
```

##### Configuration

Si vous souhaitez modifier la configuration de l'application, vous pouvez éditer le fichier suivant :

* `static\config-spa.json`

##### Génération des packages

Afin d'utiliser le mode FullSPA, il faut compiler, puis générer toutes les sources au format javascript

```shell
$ hb prepare-package
```

Le mode FullSPA nécessite d'avoir dans le répertoire 'static' les fichiers:

- messages.json
- messages-fr-FR.json
- navigation.json

Pour ce faire, une commande est disponible :

```shell
$ hb prepare-package:spa
```

##### Configuration Apache

Exemple de configuration Apache :

- `CHEMIN_ABSOLU_APPLICATION` : C:/home/workspace/HornetTest/static
- `NOM_APPLICATION`: hornettest-spa
- `NOM_APPLICATION_SERVICE`: hornettest-service

Remarque : `CHEMIN_ABSOLU_APPLICATION` doit pointer vers le répertoire qui contient la partie `static` de l'application.

```
Alias /NOM_APPLICATION "{CHEMIN_ABSOLU_APPLICATION}"
<Directory "{CHEMIN_ABSOLU_APPLICATION}">
    #Options Indexes FollowSymLinks
    #Require all granted
    Options +FollowSymLinks
    AllowOverride All
    Order deny,allow
    Allow from all
</Directory>

RewriteEngine on
# Redirection du contexte racine vers la page d'accueil
RewriteRule ^/NOM_APPLICATION/$ /NOM_APPLICATION/accueil [R]
# Reécrire les routes applicatives (les URI du type /NOM_APPLICATION/route, sans . dans le chemin) pour les renvoyer vers le client js (index.html)
RewriteRule ^/NOM_APPLICATION/[^\.]*$ /NOM_APPLICATION/index.html [PT]

<Location "/{NOM_APPLICATION_SERVICE}/">
      Header set Access-Control-Allow-Origin "*"
      ProxyPass "http://localhost:8080/{NOM_APPLICATION_SERVICE}/"
</Location>
```

##### Démarrage de l'application

Utiliser un navigateur web pour y accéder par l'url [http://localhost/nom_de_l_application](http://localhost/nom_de_l_application).
