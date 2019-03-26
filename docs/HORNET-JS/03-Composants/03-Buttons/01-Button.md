# Le composant Button

## Description 

Le bouton produit un élément de type submit.

![bouton](../sources/button/bouton.png)

## Utilisation

Il doit être initialisé avec une structure comme suit :

```javascript

let item: ButtonProps[] = {
    type: "submit",
    id: "form_btnTest",
    name: "btnTest",
    value: "test value",
    onClick : () => {console.log("onClick")},
    className: "hornet-button",
    label: "test label",
    title: "title"
};

```

| Attribut   | Description                                                       | Obligatoire | Valeur par défaut | Type |
| --------- | ------------------------------------------------------------------ |-------------|------------------ |------|
| className | Style appliqué sur le bouton                                       | &nbsp;      | &nbsp;            | string     |
| disabled  | Bouton désactivé ou non                                            | &nbsp;      | false             | boolean     |
| id        | Identifiant unique du bouton                                       | &nbsp;      | &nbsp;            | string     |
| label     | Texte sur du bouton                                                | &nbsp;      | &nbsp;            | string     |
| name      | Nom du bouton                                                      | &nbsp;      | &nbsp;            | string     |
| onClick   | Fonction exécutée sur le click                                     | &nbsp;      | &nbsp;            | React.MouseEventHandler<HTMLElement>     |
| title     | Title sur du bouton                                                | &nbsp;      | &nbsp;            | string     |
| type      | Prend les valeurs: link, button, submit, reset                     | &nbsp;      | &nbsp;            | string     |
| value     | Valeur correspondante à ce bouton pour la soumission du formulaire | &nbsp;      | &nbsp;            | string     |
| url       | Url sur laquelle redirige le bouton                                | &nbsp;      | &nbsp;            | string     |
| target    | Cible d'ouverture du lien                                          | &nbsp;      | &nbsp;            | LinkTarget |

L'objet `LinkTarget` est une énumération contenant les valeurs possibles de l'attribut `target` d'un lien html
```
export enum LinkTarget {
    BLANK = "_blank",
    SELF = "_self",
    PARENT = "_parent",
    TOP = "_top",
}
```


Exemple d'utilisation:

```javascript

import { Button } from "hornet-js-react-components/src/widget/button/button";

let props: ButtonProps[] = {
    type: "submit",
    id: "form_btnTest",
    name: "btnTest",
    value: "test value",
    onClick : () => {console.log("onClick")},
    className: "hornet-button",
    label: "test label",
    title: "title"
};

<Button
    {...props}
/>
```


Un bouton ayant la propriété url doit être de type link.

Voir la documentation de [Form](../Layouts/Form.md).

## Live coding

```javascript showroom

    let item: ButtonProps[] = {
        type: "submit",
        id: "form_btnTest",
        name: "btnTest",
        value: "test value",
        onClick : () => {alert("onClick")},
        className: "hornet-button",
        label: "test label",
        title: "title"
    };

    return <Button
        {...item}
    />
    
```
