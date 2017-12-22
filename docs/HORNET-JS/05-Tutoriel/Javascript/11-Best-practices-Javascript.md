# Best Practices Hornet

## Convention de nommage

- Les fichiers sont nommés avec des minuscules, avec un `-` comme séparateur de mots.

### Variables

- anglais
- minuscule

### Constantes

- anglais
- ALL_UPPER_CASE

### Methodes

- anglais
- verbe en minuscule, puis camelCase. exemple `moveAccount()`

### Paramètres

- anglais
- minuscule


### Double quote

Ne pas utiliser les simple quotes mais les doubles :

- Déclaration d'import
- instanciation de variable de type chaine
- ...

Ex : 

```javascript
import { Utils } from "hornet-js-utils";

var maChaine = "";
```

## Les Commentaires

Les commentaires sont écrits en français.

### Commentaires d'entête

Cartouche (voir licence).

### Ligne de commentaires

Pour une meilleure lisibilité, privilégier les commentaires documentaires en entête de fonction et les logs en debug.

### Commentaires documentaires

Identique à javadoc. En français.

## Typescript

### Import d'une classe d'un autre module

exemple :

```javascript
import { Utils } from "hornet-js-utils";
import { Logger } from "hornet-js-utils/src/logger";
import { React } from "hornet-js-react-components/src/react/hornet-react";
```
 
Afin d'affiner la norme es6 avec l'introduction des imports, il est préconiser d'utiliser les accolades dans tous les cas d'utilisation, même si un seul module est déclaré.

### Typage par inférance

Typescript permet de typer fortement sont code. Cependant, il n'est pas nécessaire de tout typer. 

Dans la pratique, il faut typer : 

- les variables de classe
- les paramètres de fonction

Exemple de bonne pratique : 

```javascript
class Partenaire {
    id:number;
    
	getId(){
		return this.id;
	}    
	setId(newId:number){
		this.id = newId;
	} 
}
```

Dans cet exemple, il est nécessaire de mettre un type sur la variable de la fonction `setId`, sinon typescript le traduit comme un type `any`. 

Exemple de mauvaise pratique : 

```javascript
let maVariable:string = "test"
```

## Base de l'isomorphisme avec React et Node.js

```
L'utilisation de frameworks JavaScript est une pratique de plus en plus répandue à l'heure actuelle. Ces derniers nous permettent de mieux organiser nos projets et d'augmenter notre productivité. On peut toutefois se retrouver coincé lorsque l'on se penche sur la question du référencement. La plupart des moteurs de recherche ne liront pas le JavaScript et, par conséquent, ne pourront pas indexer correctement les pages de votre site.

On entend parler aujourd'hui d'application isomorphe (ou universelle) dont la particularité est de pouvoir générer le rendu HTML à la fois côté client et côté serveur. Cette technique est accessible avec l'utilisation de Node.js qui nous permet de tirer profit du JavaScript côté serveur.

Dans ce tutoriel, j'illustrerai mes propos à travers un exemple en utilisant React et Node.js. React est une bibliothèque JavaScript, développée par Facebook, permettant de créer des composants qui constitueront l'interface du site. Sa particularité est de manipuler le DOM de façon intelligente en ne modifiant que le strict minimum lors du rafraîchissement des données. Cette notion apparaîtra plus clairement dans la suite de ce tutoriel.
```

http://soat.developpez.com/tutoriels/node-js/introduction-isomorphisme-react-node-js/

## HornetPage

Si des données de navigation sont passées à une page (par exemple avec la méthode `navigateTo`), il est conseillé de les nettoyer à l'aide de la méthode `deleteNavigateData`

```javascript
this.deleteNavigatedata();
```

## Render
Eviter d'avoir du code trop complexe dans les renders, cette méthode fait partie du cycle de vie react et peut être appelée plein de fois.

## Datasource et React
Eviter d'instancier les datasources dans le cycle de vie react. C'est un composant qui hérite d'eventEmitter.
Dans le contructeur de la page, c'est sa place...



# React Best Practices Hornet

## Convention de nommage composants React

- Les composants React faisant partie du framework sont prefixés par Hornet, puis le nom du composant en anglais. exemple: `HornetButton`.
- Les composants React écrits dans l'application ont un nom provenant de leur specification fonctionnelle.

Les composants React qui correspondent à des écrans doivent finir par `page`.

Exemple: `gen-cnt-page.tsx`

## React components life cycle

### Utilisation de l'héritage

Les méthodes du cycle de vie React doivent quand c'est possible hériter des méthodes du parent en invocant le super :
- componentWillMount():void;
- componentDidMount():void;
- componentWillReceiveProps(nextProps:P, nextContext:any):void;
- componentWillUpdate(nextProps: P, nextState: S, nextContext: any):void;
- componentDidUpdate(prevProps: P, prevState: S, prevContext: any):void;
- componentWillUnmount():void;

```javascript
    componentWillMount() {
        super.componentWillMount();
        //traitements propres au composant
    }
```


### Modification des states sur des composants démontés

Il est important de bien respecter le cycle de vie des composants React. On peut rapidement se retrouver à vouloir modifier les states d'un composant déjà démonté ou pas encore monté au DOM.

Ces infractions génèrent des warnings tel que celui-ci :

```console
    Warning: setState(...): Can only update a mounted or mounting component. This usually means you called setState() on an unmounted component. This is a no-op. Please check the code for the AutoCompleteField component.
```

Ces warnings doivent être corrigés. Dans la plupart des cas il faut revoir la logique des modifications des states pour ne pas se retrouver à vouloir modifier un composant démonté.
Il arrive cependant que ça ne soit pas aussi simple, ou que celà cache une mauvaise gestion des events.

#### Composant monté ou démonté ?

Une méthode isMounted() existe, elle retourne true ou false en fonction de l'état du composant. Il est fortement déconseillé par Facebook d'utilisé cette méthode qui est depréciée et devrait disparaitre d'une version à l'autre.

Si connaître l'état du composant est essentiel à la logique du composant il est recommandé d'utiliser une variable de class isMounted retournant false par défaut et mise à jour en fonction du cycle de vie :

```javascript
    componentDidMount() {
        this.isMounted = true;
    }

    componentWillUnmount() {
        this.isMounted = false;
    }
```

#### Retrait des listeners lorsqu'un composant est démonté

Les warnings sur le setState peut aussi être provoqué par des listeners qui trainent alors que le composant concerné a été démonté du DOM.

Les listeners ajoutés lorsqu'un composant est monté ou s'apprête à être monté doivent être retiré lorsque celui-ci va être démonté.

```javascript
    componentWillUnmount() {
            //retrait des listeners
        }
```

## React unique key

On peut trouver des warnings relatifs à l'unicité des react keys.

```console
    Warning: Each child in an array or iterator should have a unique "key" prop. Check the render method of `Table`. See https://fb.me/react-warning-keys [^] for more information.
```

Ils apparaissent lorsqu'un composant est contenu dans un composant de groupe. Lorsque ce type de warning apparait, il revient à la charge du développeur de préciser un key dans les composants qu'il utilise.

```javascript
    <FieldSet id="fs-type" key="fs-type" legend={formI18n.type}>
        //contenu
    </FieldSet>
```