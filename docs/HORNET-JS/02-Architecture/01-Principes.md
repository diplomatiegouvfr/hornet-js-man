# Principes

## Principes & définitions

### Node.js

> **Node.js** est une plateforme logicielle libre et événementielle en JavaScript orientée vers les applications réseau qui doivent pouvoir monter en charge. Elle utilise la machine virtuelle V8 et implémente sous licence MIT les spécifications CommonJS. Node.js contient une bibliothèque de serveur HTTP intégrée, ce qui rend possible de faire tourner un serveur web sans avoir besoin d'un logiciel externe comme Apache ou Lighttpd, et permettant de mieux contrôler la façon dont le serveur web fonctionne.

[http://fr.wikipedia.org/wiki/Node.js](http://fr.wikipedia.org/wiki/Node.js)

### JavaScript

> **JavaScript** est un langage de programmation de scripts principalement employé dans les pages web interactives mais aussi pour les serveurs. C’est un langage orienté objet à prototype, c’est-à-dire que les bases du langage et ses principales interfaces sont fournies par des objets qui ne sont pas des instances de classes, mais qui sont chacun équipés de constructeurs permettant de créer leurs propriétés, et notamment une propriété de prototypage qui permet d’en créer des objets héritiers personnalisés. En outre, les fonctions sont des objets de première classe.
>
> Le langage a été créé en 1995 par Brendan Eich (Brendan Eich étant membre du conseil d'administration de la fondation Mozilla à cette époque) pour le compte de Netscape Communications Corporation. Le langage, actuellement à la version 1.8.2, est une implémentation de la 3e version de la norme ECMA-262 qui intègre également des éléments inspirés du langage Python. La version 1.8.5 du langage est prévue pour intégrer la 5e version du standard ECMA.

[http://fr.wikipedia.org/wiki/JavaScript](http://fr.wikipedia.org/wiki/JavaScript)

#### Fonctions de retour (callback functions)

Une `callback` (fonction de retour) est une fonction dont la particularité est d'être transmise en paramètre d'une autre fonction qui se chargera de l'appeler. Ce mécanisme permet notamment de rendre configurable un traitement par injection d'un comportement variable, ou de déclencher un traitement ultérieurement.

En JavaScript, le passage de fonction en paramètre se fait en donnant simplement son nom sans ajouter les parenthèses.

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


#### Promise

La notion de `promise` permet une autre forme d'écriture des appels de fonction avec callback. En effet, l'imbrication de fonction de retour peut rendre le code difficile à lire, soit on alourdit le code en déclarant les fonctions directement les unes dans les autres, soit on perd de vue l'enchaînement des traitements (callback hell). L'écriture sous forme de `promise` permet d'améliorer cela :

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
##### CancellablePromise

Il existe également une notion de promesse annulable dans Hornet.js.
Ces promesses peuvent être annulées avant leur retour d'exécution. Elles peuvent être soit nommées, soit anonymes. Les promesses nommées pouront être annulées à n'importe quel moment de façon unitaire. Les promesses anonymes ne pourront être annulées que toutes en même temps.

Elles s'utilisent de la façon suivante:

```javascript
import { CancellablePromise } from "hornet-js-utils/src/cancellable-promise";

// Promesse nommée
const promiseNommee =  CancellablePromise.getNamedPromise((resolve, reject, onCancel) => {
    ...
    onCancel(() => {...});
}, "nom");

// Promesse anonyme
const promise =  CancellablePromise.getPromise((resolve, reject, onCancel) => {
    ...
    onCancel(() => {...});
});

// Annulation de toutes les promesses, nommées ou non
CancellablePromise.clearAllCancellablePromises(true);

//Annulation des promesses non nommées
CancellablePromise.clearAllCancellablePromises(false);

//Annulation des promesses des promesses nommées
CancellablePromise.clearAllNamedPromises();

//Annulation d'une promesse nommée
CancellablePromise.cancelPromiseWithName("nom")
```


### TypeScript

> **TypeScript** est un langage de programmation libre et open-source développé par Microsoft qui a pour but d'améliorer et de sécuriser la production de code JavaScript. C'est un sur-ensemble de JavaScript (c'est-à-dire que tout code JavaScript correct peut être utilisé avec TypeScript). Le code TypeScript est transcompilé en JavaScript, pouvant ainsi être interprété par n'importe quel navigateur web ou moteur JavaScript. Il a été cocréé par Anders Hejlsberg, principal inventeur de C#.
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
import ServiceApi = require("hornet-js-core/src/services/ServiceApi");

class ContactApi extends ServiceApi {
    send(data) {
        // ... implémentate de la méthode
    }
}
export = ContactApi;
```

### JSON

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

### HTML 5

> **HTML5** (HyperText Markup Language 5) est la dernière révision majeure d'HTML (format de données conçu pour représenter les pages web). Cette version a été finalisée le 28 octobre 2014. HTML5 spécifie deux syntaxes d'un modèle abstrait défini en termes de DOM : HTML5 et XHTML5. Le langage comprend également une couche application avec de nombreuses API, ainsi qu'un algorithme afin de pouvoir traiter les documents à la syntaxe non conforme. Le travail a été repris par le W3C en mars 2007 après avoir été lancé par le WHATWG. Les deux organisations travaillent en parallèle sur le même document afin de maintenir une version unique de la technologie. Le W3C vise la clôture des ajouts de fonctionnalités le 22 mai 2011 et une finalisation de la spécification en 2014, et encourage les développeurs Web à utiliser HTML 5 dès maintenant.

[http://fr.wikipedia.org/wiki/HTML5](http://fr.wikipedia.org/wiki/HTML5)


### SCSS et préprocesseurs

> **Préprocesseurs** Comme son nom l'indique, un préprocesseur CSS intervient avant le traitement des fichiers CSS par les navigateurs web. Il se situe soit côté serveur, soit côté client. Il permet de générer dynamiquement un fichier CSS standard (aux normes W3C) à partir d'un ou de plusieurs fichiers source qui disposent d'une syntaxe plus étendue que le CSS.

Il existe plusieurs préprocesseurs CSS, chacun possédant une syntaxe et un langage qui leur est propre. Bien qu'ils étendent la structure du CSS, ils restent indépendants des normes CSS existantes. Parmi les plus connus se trouvent Sass, LESS et Stylus3. L'objectif des préprocesseurs CSS est d'ajouter des fonctionnalités qui permettent au développeur de mieux structurer son code, fonctionnalités qui pour beaucoup manquent au CSS. En créant une couche d'abstraction, elles visent à améliorer la maintenabilité et la clarté du code, à améliorer sa structure, à éviter les répétitions (philosophie de programmation "DRY"), et ainsi à augmenter la productivité du développeur. Quelques-unes des fonctionnalités offertes par les préprocesseurs CSS incluent généralement : les variables, l’imbrication (hiérarchisation) du code, les mixins, les opérations mathématiques et logiques, les conditions, les boucles et les importations.

[https://fr.wikipedia.org/wiki/Pr%C3%A9processeur_CSS](https://fr.wikipedia.org/wiki/Pr%C3%A9processeur_CSS)

#### History

> L'objet **DOM** `window` fournit un accès à l'historique du navigateur via l'objet history. Il expose un ensemble de méthodes et de propriétés qui permettent d'avancer et de reculer dans l'historique de l'utilisateur ainsi que -- à partir d'HTML5 -- manipuler le contenu de l'ensemble de l'historique.

[https://developer.mozilla.org/fr/docs/Web/Guide/DOM/Manipuler_historique_du_navigateur](https://developer.mozilla.org/fr/docs/Web/Guide/DOM/Manipuler_historique_du_navigateur)

Hornet utilise la manipulation de l'historique du navigateur. Cela permet :

* de conserver un historique de navigation (fonction back)
* de copier et sauvegarder des liens directs vers une page de l'application

### Architecture REST

> **REST** (REpresentational State Transfer) est un style d’architecture pour les systèmes hypermédia distribués, créé par Roy Fielding en 2000 dans le chapitre 5 de sa thèse de doctorat1.
>
> REST n’est pas un protocole (tel que HTTP) ou un format. Ce style d'architecture est particulièrement bien adapté au World Wide Web mais n'en est pas dépendant. Les contraintes, telles que définies par Roy Fielding, peuvent s'appliquer à d'autres protocoles d'application que HTTP.

[http://fr.wikipedia.org/wiki/Representational_State_Transfer](http://fr.wikipedia.org/wiki/Representational_State_Transfer)

L'implémentation des services REST dans le cadre d'un module applicatif de service d'Hornet s'appuie sur le protocole http (notamment sur les verbes et les codes de réponses).

Exemples de « signatures » http de services REST d'un module applicatif Hornet Service :

| Verbe  | URL         | Description |
| ------ | ----------- | ----------- |
| GET    | /clients    | Charge la liste des clients |
| GET    | /clients/1  | Charge le détail du client ayant l'identifiant '1' |
| POST   | /clients    | Créé un client |
| PUT    | /clients/1  | Mise à jour complète d'un client |
| PATCH  | /clients/1  | Mise à jour partielle d'un client |
| DELETE | /clients/1  | Supprime le client ayant l'identifiant '1' |


### Séparation des rôles

Les pages web sont composées de trois parties :

* La partie sémantique en html ou xhtml (le fond),
* La partie présentation avec des styles CSS (la forme),
* La partie interactive, dynamique et événementielle avec du JavaScript (la forme).

Ces trois couches utilisées pour les pages web sont détaillées dans les paragraphes suivants.

#### Partie sémantique

Cette partie est implémentée sous forme de document HTML ou XML conformément aux normes w3c et en respectant les règles d’accessibilité.

Aucun élément de présentation ne doit être présent dans ces documents (hormis les liens d’identification et de  classes CSS pour la présentation).

Les éléments HTML doivent être utilisés conformément à leur usage prévu par le w3c, par exemple :

* Les « tables » HTML doivent être utilisées essentiellement pour présenter des données sous forme de tableaux, et non pour structurer l’affichage.

#### Partie Présentation

La partie présentation est réalisée via les styles CSS et les éléments HTML de type groupement (div, p, span …).

La partie présentation fait aussi appel à la composition. Les briques utilisées par le framework permettent de composer les écrans à partir de composants plus fins.

Le framework fournit les briques de bases, ainsi que des templates d'écran type via l'`applitutoriel`


#### Partie Interactive

La partie interactive est réalisée en JavaScript/TypeScript.
