# Le composant HornetComponent

## Description

Le composant `Component` est le composant de plus "haut-niveau" du framework `Hornet`.
La classe `HornetComponent` étend la classe `React.Component`.
Tout composant déclaré dans `hornet-js-react-components` hérite de `HornetComponent`.

### Méthodes / attributs

#### User

l'attribut `user` permet de récupérer le profil du user connecté

#### Manipulation de composants


| Nom Méthode | Description |
|-------------|-------------|
|wrapMethod|Cloner un élément étant très `coûteux` (niveau performance) en React via la méthode `cloneElement`, cette méthode permet d'enrober un composant.|
|getChildrenOf|Retourne les éléments enfants d'un composant du type passé en paramètre|
|getComponentBy|Retourne l'élément enfant d'un composant du type passé en paramètre|
|hasChildrenOfComponentTypeOf|Méthode déterminant si un des enfant dont le parent est de type Component est de type ComponentType|
|wrap|Wrap un composant avec de nouvelles props pour éviter le cloneElement|



#### Gestion des évènements

| Nom Méthode | Description |
|-------------|-------------|
|listen|Permet d'écouter un évènement|
|listenOnce|Permet de n'écouter qu'une seule foix un évènement|
|fire|Permet d'émettre un évènement|
|remove|Permet de supprimer un évènement|

#### Gestion des urls

| Nom Méthode | Description |
|-------------|-------------|
|genUrlTheme|Méthode permettant de construire l'url d'une image se trouvant dans le thème embarqué par l'application|
|genUrlThemeExternal|Méthode permettant de construire l'url d'une image d'un thème embarqué externe|
|genUrl|Méthode permettant de générer une Url applicative|
|genUrlWithParams|Permet de générer une Url de l'application depuis une url paramétrée|


```javascript

let path = "/partenaire/:id";
let item = {
    id: 78,
    name: "martin"
};

let url = this.genUrlWithParams(url, item)

// => url = /partenaire/78

```



#### Internationalisation

| Nom Méthode | Description |
|-------------|-------------|
|getInternationalization|Permet de récupérer tous les messages liés à l'internationalisation ainsi que la local|
|i18n|Renvoie le ou les messages internationalisés correspondant à la clé indiquée, après avoir remplacé les valeurs paramétrables avec celles indiquées|

```javascript

let message = "Consulter la fiche de {nom} {prenom}"

let label = this.i18n(message, {
            nom: "martin",
            prenom: "paul"
        });
        
// => Consulter la fiche de martin paul


```

#### Gestion des erreurs

| Nom Méthode | Description |
|-------------|-------------|
|getErrorComponent|Méthode retournant la page à afficher en cas d'erreur|
|getErrorHandler|Méthode permettant de retourner la fonction implémentant le comportement à adopter en cas d'erreur|

### Création d'un HornetComponent

Code de base de création d'un HornetComponent :

```javascript
import {HornetComponentProps} from "hornet-js-components/src/component/ihornet-component";
import {HornetComponent} from "src/widget/component/hornet-component";

export interface MyComponentProps extends HornetComponentProps {
    ...
}

export class MyComponent extends HornetComponent<MyComponentProps, any> {
    ...
}
```