# Principes & définitions

## JavaScript

> JavaScript est un langage de programmation de scripts principalement employé dans les pages web interactives mais aussi pour les serveurs. C’est un langage orienté objet à prototype, c’est-à-dire que les bases du langage et ses principales interfaces sont fournies par des objets qui ne sont pas des instances de classes, mais qui sont chacun équipés de constructeurs permettant de créer leurs propriétés, et notamment une propriété de prototypage qui permet d’en créer des objets héritiers personnalisés. En outre, les fonctions sont des objets de première classe.
>
> Le langage a été créé en 1995 par Brendan Eich (Brendan Eich étant membre du conseil d'administration de la fondation Mozilla à cette époque) pour le compte de Netscape Communications Corporation. Le langage, actuellement à la version 1.8.2, est une implémentation de la 3e version de la norme ECMA-262 qui intègre également des éléments inspirés du langage Python. La version 1.8.5 du langage est prévue pour intégrer la 5e version du standard ECMA.

[http://fr.wikipedia.org/wiki/JavaScript](http://fr.wikipedia.org/wiki/JavaScript)

### Fonctions de retour (callback functions)

Une `callback` (fonction de retour) est une fonction dont la particularité est d'être transmisse en paramètre d'une autre fonction qui se chargera de l'appeller. Ce mécanisme permet notamment de rendre configurable un traitement par injection d'un comportement variable, ou de déclencher un traitement ultérieurement.

En JavaScript, le passage de fonction en paramètre se fait en donnant simplement son nom sans ajouter les paranthères.

Exemple d'utilisation de fonction JavaScript reçu en paramètre d'une autre :

``` javascript
function job(callback) {
    // ... code du comportement de la fonction 'job'
    callback(); // appel de la fonction transmise
}
function next() {
    // ...
}
function main() {
    // passage de la fonction next en paramètre de l'appel de la fonction job
    job(next);
}
```


### Promesse/Promise

La notion de `promise` permet une autre forme d'écriture des appels de fonction avec callback. En effet, l'imbrication de fonction de retour peut rendre le code difficile à lire, soit on alourdi le code en déclarant les fonctions directement les unes dans les autres, soit on perd de vue l'enchainnement des traitements. L'écriture sous forme de promesses permet d'améliorer cela :

``` javascript
promise
    .then(function(result) {
        // ... Premier traitement si OK
    })
    .then(function(result) {
        // ... Second traitement si toujours OK
    })
    .fail(function(err) {
        // ... Traitement si erreur
    }
```

### Mixin

> Un mixin ou une classe mixin est une classe destinée à être composée par héritage multiple avec une autre classe pour lui apporter des fonctionnalités. C'est un cas de réutilisation d'implémentation. Chaque mixin représente un service qu'il est possible de greffer aux classes héritières.

[http://fr.wikipedia.org/wiki/Mixin](http://fr.wikipedia.org/wiki/Mixin)

## TypeScript

> TypeScript est un langage de programmation libre et open-source développé par Microsoft qui a pour but d'améliorer et de sécuriser la production de code JavaScript. C'est un sur-ensemble de JavaScript (c'est-à-dire que tout code JavaScript correct peut être utilisé avec TypeScript). Le code TypeScript est transcompilé en JavaScript, pouvant ainsi être interprété par n'importe quel navigateur web ou moteur JavaScript. Il a été cocréé par Anders Hejlsberg, principal inventeur de C#.
>
> TypeScript permet un typage statique optionnel des variables et des fonctions, la création de classes et d'interfaces, l'import de modules, tout en conservant l'approche non-contraignante de JavaScript. Il supporte la spécification ECMAScript 6.

[http://fr.wikipedia.org/wiki/TypeScript](http://fr.wikipedia.org/wiki/TypeScript)

Exemples de fichiers TypeScript (simplifiés) :

* `ServiceApi.ts` :

``` typescript
class ServiceApi {
    request() {
        return superagent;
    }

    buildUrl(path) {
        return host + name + path;
    }
}
export = ServiceApi;
```

* `ContactApi.ts` :  

``` typescript
import ServiceApi = require('hornet-js-core/src/services/ServiceApi');

class ContactApi extends ServiceApi {
    send(data) {
        // ... implémentate de la méthode
    }
}
export = ContactApi;
```

## JSON

> **JSON** (JavaScript Object Notation) est un format de données textuelles dérivé de la notation des objets du langage JavaScript. Il permet de représenter de l’information structurée comme le permet XML par exemple. Créé par Douglas Crockford entre 2002 et 2005, il est décrit par la RFC 7159 de l’IETF.

[http://fr.wikipedia.org/wiki/JavaScript_Object_Notation](http://fr.wikipedia.org/wiki/JavaScript_Object_Notation)

Exemple de donnée au format JSON :

``` JSON
{
  "prop_A" : {
    "prop_A1" : "valeur",
    "prop_A2" : 10
  },
  "prop_B" : [
    "valeur B1",
    "valeur B2",
    "valeur B3"
  ],
  "prop_C" : [
    {
      "prop_Ca1" : "valeur",
      "prop_Ca2" : "valeur"
    },
    {
      "prop_Cb1" : "valeur",
      "prop_Cb2" : "valeur"
    }
  ]
}
```

## HTML 5

> HTML5 (HyperText Markup Language 5) est la dernière révision majeure d'HTML (format de données conçu pour représenter les pages web). Cette version a été finalisée le 28 octobre 2014. HTML5 spécifie deux syntaxes d'un modèle abstrait défini en termes de DOM : HTML5 et XHTML5. Le langage comprend également une couche application avec de nombreuses API, ainsi qu'un algorithme afin de pouvoir traiter les documents à la syntaxe non conforme. Le travail a été repris par le W3C en mars 2007 après avoir été lancé par le WHATWG. Les deux organisations travaillent en parallèle sur le même document afin de maintenir une version unique de la technologie. Le W3C vise la clôture des ajouts de fonctionnalités le 22 mai 2011 et une finalisation de la spécification en 2014, et encourage les développeurs Web à utiliser HTML 5 dès maintenant.

[http://fr.wikipedia.org/wiki/HTML5](http://fr.wikipedia.org/wiki/HTML5)

### History

> L'objet DOM `window` fournit un accès à l'historique du navigateur via l'objet history. Il expose un ensemble de méthodes et de propriétés qui permettent d'avancer et de reculer dans l'historique de l'utilisateur ainsi que -- à partir d'HTML5 -- manipuler le contenu de l'ensemble de l'historique.

[https://developer.mozilla.org/fr/docs/Web/Guide/DOM/Manipuler_historique_du_navigateur](https://developer.mozilla.org/fr/docs/Web/Guide/DOM/Manipuler_historique_du_navigateur)

Hornet JS 5.0 utilise la manipulation de l'historique du navigateur (cf. paragraphe ci-après). Cela permet :

* de conserver un historique de navigation ;
* de copier et sauvegarder des liens directs vers une page de l'application.

## Architecture REST

> **REST** (REpresentational State Transfer) est un style d’architecture pour les systèmes hypermédia distribués, créé par Roy Fielding en 2000 dans le chapitre 5 de sa thèse de doctorat1.
>
> REST n’est pas un protocole (tel que HTTP) ou un format. Ce style d'architecture est particulièrement bien adapté au World Wide Web mais n'en est pas dépendant. Les contraintes, telles que définies par Roy Fielding, peuvent s'appliquer à d'autres protocoles d'application que HTTP.

[http://fr.wikipedia.org/wiki/Representational_State_Transfer](http://fr.wikipedia.org/wiki/Representational_State_Transfer)

L'implémentation des services REST dans le cadre d'un module applicatif de service d'Hornet JS s'appuie sur le protocole http (notamment sur les verbes et les codes de réponses).

Exemples de « signatures » http de services REST d'un module applicatif Hornet Service :

| Verbe  | URL         | Description |
| ------ | ----------- | ----------- |
| GET    | /clients    | Charge la liste des clients |
| GET    | /clients/1  | Charge le détail du client ayant l'identifiant '1' |
| POST   | /clients    | Créé un client |
| PUT    | /clients/1  | Mise à jour complète d'un client |
| PATCH  | /clients/1  | Mise à jour partielle d'un client |
| DELETE | /clients/1  | Supprime le client ayant l'identifiant '1' |
[Services Rest]

## Isomorphisme

> Isomorphic JavaScript apps are JavaScript applications that can run both client-side and server-side.
The backend and frontend share the same code.

[http://isomorphic.net/](http://isomorphic.net/)

Cette philosophie de développement permet de mutualiser le code entre le serveur et le navigateur web. L'objectif est de limiter les doubles développements tout en permettant un fonctionnement sans JavaScript sur les postes client.

Les codes spécifiques à l'un ou l'autre mode permettent d'assurer des fonctionnements aussi proches que possibles.

## Séparation des rôles

Les pages web sont composées de trois parties :

* La partie sémantique en html ou xhtml (le fond),
* La partie présentation avec des styles CSS (la forme),
* La partie interactive, dynamique et événementielle avec du JavaScript (la forme).

Ces trois couches utilisées pour les pages web sont détaillées dans les paragraphes suivants.

### Partie sémantique

Cette partie est implémentée sous forme de document HTML ou XML conformément aux normes w3c et en respectant les règles d’accessibilité.

Aucun élément de présentation ne doit être présent dans ces documents (hormis les liens d’identification et de  classes CSS pour la présentation).

Les éléments HTML doivent être utilisés conformément à leur usage prévu par le w3c, par exemple :

* Les « tables » HTML doivent être utilisées essentiellement pour présenter des données sous forme de tableaux, et non pour structurer l’affichage.

### Partie Présentation

La partie présentation est réalisée via les styles CSS et les éléments HTML de type groupement (div, p, span …).

### Partie Interactive

La partie interactive est réalisée en JavaScript/TypeScript à l’aide du pattern Flux.

# Fonctionnement

Le fonctionnement, vu par l’utilisateur dans un navigateur web, est similaire à celui d’un site web classique. Cependant, il y a un certain nombre de différences au niveau de la structure des pages web et de l’emploi des composants :

* Les pages web peuvent être utilisées sans JavaScript, sans images (et autres informations visuelles) ou sans styles CSS,
* Chaque document HTML est conforme aux normes w3c et utilise les éléments et attributs HTML adéquats avec l’information véhiculée.

Deux modes d’utilisation sont possibles :

1. Multiple Page Application (MPA, « web 1.0 ») :  
	- L’utilisateur accède à des pages HTML utilisant des styles, des images et chaque clic sur un lien ou validation de formulaire entraine le chargement complet d’une nouvelle page dans le navigateur,
2. Single Page Application (SPA) :  
	- Dans le mode SPA, toutes les actions sont interceptées et remplacées par des appels Ajax modifiant partiellement le DOM de la page, il n’y a alors plus de chargement complet de page.  
	- Ce mode permet une grande richesse et interactivité des applications web, mais il nécessite un cadre de conception et développement uniformisé.

Les paragraphes suivants présentent de façon plus détaillées les caractéristiques essentielles de la solution.

## Mode Single Page Application (SPA)

Dans le mode SPA, seuls les éléments impactés par un changement de fonction sont modifiés à l’aide de traitements JavaScript, les autres parties, comme l’entête ou le menu, restent inchangées).

Dans ce mode, les traitements sont majoritairement exécutés dans le navigateur client en JavaScript. Les appels aux serveurs servent à interagir avec les données persistées sur le serveur distant (création, consultation, modification, suppression). Ces échanges sont réalisés à l’aide d’appels Ajax.

L’exemple suivant illustre le fonctionnement SPA par rapport au mode MPA :

* Cinématique MPA :

  1. L’utilisateur demande la première page de l'application.
  1. L’application retourne un document HTML complet (avec entête, menu, corps, bas de page).
  1. L’utilisateur demande à accéder à la page suivante en cliquant sur un lien hypertexte, le navigateur émet une requête http pour afficher la page correspondante,
  1. L’application retourne un document HTML complet (avec entête, menu, corps, bas de page).
  1. L’utilisateur demande à accéder à des données, le navigateur émet la requête http correspondante,
  1. L’application retourne un document HTML complet (avec entête, menu, corps, bas de page) avec les données.
  
* Cinématique SPA :

  1. L’utilisateur demande la première page de l'application.
  1. L’application retourne un document HTML complet (entête, menu, corps, bas de page). Le mode SPA s’initialise dans le navigateur par l'exécution automatique de JavaScript (rehydratation des Stores, initialisation du court-circuit des click...).
  1. Dans mode SPA, les liens vers les autres écrans (navigation) sont court-circuités et remplacés par des navigations internes avec du code JavaScript, l'accès aux données se fait par la déclaration de routes dites **Data** ([Routeur](#Routeur))<br>
    - L’utilisateur demande à accéder à la page suivante en cliquant sur le lien hypertexte correspondant. Le clic ayant été détourné par le mode SPA, le code JavaScript émet un changement de route interne.<br>
    - L’utilisateur demande à accéder à des données, c'est une route *Data* qui est sollicitée. Ce qui soumet un appel AJAX pour récupérer un flux JSON. On met à jour des [Stores](#Stores) ce qui provoque le rafraîchissement de certains composants de la page grace à l'utilisation de [React](#Vues).

Chaque fragment HTML, correspondant aux éléments de page, est conçu sous forme de composant pour être indépendant de son conteneur.[Rendu Client](#rendu_client)



# Architecture du framework

## Introduction

Ce document présente l’architecture du Framework Hornet dans sa version Isomorphe (5.0) et des applications qui l’utilisent.

## Architecture technique

L'architecture de Hornet 5 s'appuie principalement sur les éléments suivants :

1. Un module applicatif de services en technologie Java : Hornet Services.
2. Un module applicatif de présentation en technologie JavaScript/TypeScript : Hornet JS.

![Architecture générale](./sources/architecture/architecture-generale.png)

### Hornet Services

Le module applicatif de services a pour fonction d'exposer sous forme d'API REST les données et services métier de l'application.

Ce module est développé en technologie Java et déployé dans le conteneur de servlet Tomcat.

#### Architecture logique

![Architecture logique](./sources/architecture/architecture-logique-services.png)

Les principaux composants techniques intervenant dans le module applicatif de services sont :

* Java Virtual Machine 8 ;
* Conteneur de Servlet Tomcat 8 ;
* Injection de dépendances avec Spring 4 ;
* Exposition des services REST avec Spring MVC 4 ;
* Accès à la base de données avec myBatis 3.

### Hornet JS

Le module applicatif de présentation a pour fonction de fournir l'IHM de l'application.

Ce module est développé en technologie JavaScript/TypeScript et s'exécute grâce à Node.js.

> Node.js est une plateforme logicielle libre et événementielle en JavaScript orientée vers les applications réseau qui doivent pouvoir monter en charge. Elle utilise la machine virtuelle V8 et implémente sous licence MIT les spécifications CommonJS. Node.js contient une bibliothèque de serveur HTTP intégrée, ce qui rend possible de faire tourner un serveur web sans avoir besoin d'un logiciel externe comme Apache ou Lighttpd, et permettant de mieux contrôler la façon dont le serveur web fonctionne.

[http://fr.wikipedia.org/wiki/Node.js](http://fr.wikipedia.org/wiki/Node.js)

#### Architecture logique

Le framework Hornet 5.0 est conçu selon le principe d'isomorphisme, la même application s'exécutera aussi bien dans un navigateur web que dans un serveur. Toutefois, les mises en oeuvre peuvent légèrement différer dans les implémentations pour prendre en compte les spécificités de ces environnements.

Les principaux composants techniques intervenant dans le module applicatif de présentation sont :

* Node.js : plateforme logiciel d'exécution du code JavaScript côté serveur ;
* Express : bibliothèque de base pour l'écriture d'application web sur Node.js ;
* Director : composant de gestion du routage des urls sur le serveur ;
* Fluxible : composant permettant de structurer les interfaces entre les modules Flux (Actions, Stores, Vues et Dispatcher)
* Dispachr : composant intégré à Fluxible orienté évènement/message prenant le rôle du module Dispatcher du pattern Flux ;
* Handlebars : moteur de *templating* en JavaScript ;
* React : bibliothèque de création d'IHM html selon une logique orientée composants ;
* Newforms : bibliothèqye JavaScript isomorphe de gestion de formulaires pour React.
* Superagent : composant JavaScript pour l'exécution d'appels http ;
* Webpack : outil de création de paquetages (JavaScript, CSS, ...) pour les navigateurs web ;
* Gulp : outil pour la création de tâches de développement.

##### Navigateur web

![Architecture logique côté navigateur](./sources/architecture/architecture-logique-js.png)

##### NodeJS

![Architecture logique côté NodeJS](./sources/architecture/architecture-logique-nodejs.png)

Dans Node.js les interactions proviennent de requêtes http, celles-ci sont prises en charge par le framework Express avant d'intégrer le déroulement du pattern Flux.

##### Pattern d’architecture Flux

Ces schémas d’architecture correspondent à l'implémentation du pattern Flux choisi pour la mise en oeuvre d’Hornet 5.0. Celui-ci ce présente comme suit :

![Pattern Flux](./sources/architecture/pattern-flux.png)

Nous allons voir ci-dessous les composants qui vont permettre de remplir les différentes responsabilités définies dans le pattern Flux.

###### Actions

Les actions portent les traitements de l’application. Leur réalisation est à la charge du développeur de l’application.

Les services externes sont appelés depuis les actions afin d’effectuer les traitements demandés par l’utilisateur. Les retours des services externes sont transmis aux *stores* par le biais du mécanisme de propagation via le  *dispatcher*.

- _Les interfaces avec les modules du pattern Flux_

Fluxible limite le contexte d'exécution des Actions à un `ActionContext` ayant l'interface suivante :

``` java
declare class ActionContext {
    dispatch(action:String, data?:any):void;
    executeAction(action:any, payload:any, callback:any):void;
    getStore(store:typeof Store): Store;
}
```
Une action partage donc une interface avec :

- Le dispatcher
- Les actions
- Les stores

- _Implémentation technique_

L’isomorphisme implique les contraintes suivantes sur ces composants :

* chaque *action* (au sens JavaScript) doit être une fonction retournant une `Promise` effectuant l’action à proprement parler (au sens métier). Ce fonctionnement est nécessaire afin de permettre au routeur :
  * de savoir quand effectuer le rendu de la page côté serveur en s’enregistrant en callback.
  * de pouvoir désactiver les actions lors du premier affichage de la page côté client pour éviter un double appel des API externes (serveur puis client).

###### Dispatcher

Ce composant technique a pour vocation à recevoir les évènements émis par les *actions* aux différents *stores* qui en sont à l’écoute. Ce composant est pris en charge par le framework via la bibliothèque JavaScript [Yahoo Fluxible](https://github.com/yahoo/fluxible) qui intègre elle-même [Yahoo Dispatchr](https://github.com/yahoo/dispatchr).

Le pattern Flux impose les contraintes suivantes sur ce composant :

* Un seul évènement propagé à la fois. Cela implique :
  * que seule une action peut émettre un évènement
  * qu’une action ne peut pas émettre un deuxième évènement avant la fin du premier
  * qu’une vue ne peut pas exécuter une action suite à un évènement émis par un store
* L’exécution des *stores* associés à un événement est synchrone
* Chaque *store* doit s’enregistrer auprès du dispatcher au chargement de l'application

L’isomorphisme impose les contraintes suivantes sur ce composant :

* Le *dispatcher* doit être instancié pour chaque requête, de ce fait il doit être propagé à tous les composants qui l’utilisent (vue, actions, routeur).
* Le *dispatcher* doit proposer une méthode pour sauvegarder l’état des *stores* côté serveur et une pour les réinjecter côté client. Ceci est nécessaire afin d’éviter un double appel des services externes lors du premier chargement de la page (rendu par le serveur puis par le client).

Ces points sont gérés par Fluxible.

###### <a id="Stores"></a>Stores

Les stores ont pour vocation à garder en mémoire l'état courant (données techniques et métiers) de l’application.

Ils ne doivent pas appeler d’API externes (rôle dévolu aux *actions*). Les vues appellent ces composants afin de construire leur rendu quand celui-ci est dynamique.

- _Les interfaces avec les modules du pattern Flux_

Fluxible limite le contexte d'exécution des Stores à un `StoreContext` ayant l'interface suivante :

``` java
declare class StoreContext {

}
```
Un Store ne partage donc aucune interface avec les autres modules du pattern Flux.

- _Implémentation technique_

* Ils doivent étendre `EventEmitter` afin de permettre aux vues de s’enregistrer

L’isomorphisme impose la contrainte suivante sur ces composants :
* Ils doivent implémenter des mécanismes de sérialisation/désérialisation afin de permettre une transmission de leur état interne du serveur vers le client. Ces méthodes sont appelées automatiquement par le dispatcher

###### <a id="Routeur"></a>Routeur

Ce composant est le point central de la navigation. Il permet de gérer de manière identique la navigation au sein de l’application que ce soit côté client (avec ou sans JavaScript) ou côté serveur.

Ce composant est configuré à partir d’un ensemble de « routes » qui viennent faire le lien entre une URL et les actions du pattern Flux (dans son implémentation isomorphe).

Le routeur d'Hornet JS 5.0 s'appuie sur le composant [Director](https://github.com/flatiron/director).

L’isomorphisme impose les contraintes suivantes sur ce composant :

* Impossible d’utiliser un même chemin d'url pour deux verbes différents (`GET`, `POST`) car la partie client ne fait pas de distinction (le framework prévoit toutefois l'utilisation du mot `both` pour indiquer que les deux verbes sont autorisés.
* Côté client : nécessité de transmettre les données `POST` par une méthode spécifique afin de ne pas modifier l’url du navigateur
* Obligation d’utiliser le mode « historique » html5 (mode `pushState` à la place de la notation `!#`) afin d’uniformiser les urls entre la partie client et serveur.
* Les actions à effectuer sur une route ne doivent pas être exécutées par la route mais par le moteur en lui-même afin que celui-ci décide si elles doivent être exécutées ou pas (voir le paragraphe sur les actions ci-dessus)

###### <a id="Vues"></a>Vues

Les vues sont les composants servant à produire le code html affiché à l'utilisateur.

- _Les interfaces avec les modules du pattern Flux_

Fluxible limite le contexte d'exécution des composants à un `ComponentContext` ayant l'interface suivante :

``` java
declare class ComponentContext {
    executeAction(action:any, payload:any):void;
    getStore(store:typeof Store): Store;
}
```
Une vue partage donc une interface avec :

- Les actions
- Les stores


- _Implémentation technique_

Le pattern Flux impose la contrainte suivante sur les vues :

* Elles ne doivent pas modifier directement l'état d'un *store*, seulement y lire des données.
* Elles peuvent exécuter des actions mais ne peuvent pas attendre de callback indiquant que l'action est terminée.

Les composants de cette brique utilisent le moteur de rendu [React](http://facebook.github.io/react/) :

* Un rendu des composants à partir d’un template JSX/JavaScript
* Une gestion des évènements permettant un binding unidirectionnel (vue -> modèle).

Dans Hornet JS 5.0, la librairie [handlebars](http://handlebarsjs.com/) gère la structure de base de la page (balises `html`, `head`, `body`, imports CSS, ect.). Les composants graphiques sont implémentés avec la librairie React et les formulaires sont décrits avec [Newforms](https://github.com/insin/newforms).

##### Diagrammes de séquence

Ci-dessous sont présentés les schémas généraux de déroulements des échanges dans le pattern Flux implémenté dans Hornet en mode SPA (navigateur web) et MPA (serveur NodeJS).

###### Suite des actions pour le rendu côté serveur

![Pattern Flux - Rendu d'une page côté serveur](./sources/architecture/pattern-flux-rendu-serveur.png)

###### <a id="rendu_client"></a>Suite des actions pour le rendu côté client

![Pattern Flux - Rendu d'une page côté serveur](./sources/architecture/pattern-flux-rendu-client.png)
