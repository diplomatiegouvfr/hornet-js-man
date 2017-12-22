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

| attribut   | description                                                        |
| --------- | ------------------------------------------------------------------ |
| className | Style appliqué sur le bouton                                       |
| disabled  | Bouton désactivé ou non                                            |
| hasPopUp  | [Accessibilité] Indicateur si le bouton ouvre un popup             |
| id        | Identifiant unique du bouton                                       |
| label     | Texte sur du bouton                                                |
| name      | Nom du bouton                                                      |
| onClick   | Fonction exécutée sur le click                                     |
| title     | Title sur du bouton                                                |
| type      | Le type input html submit, button, radio, checkbox, etc            |
| value     | Valeur correspondante à ce bouton pour la soumission du formulaire |
| url       | Url sur laquelle redirige le bouton                                |
[Button - attributs]

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


Voir la documentation de [Form](../Layouts/Form.md).

## Accessibilité

Dans le cas où le bouton sert à ouvrir un popup, la propriété `hasPopUp` doit être valorisée à true.

Le button html aura alors l'attribut `aria-haspopup` à true.

Cette pratique est obligatoire dans le respect de l'accessibilité pour les boutons ouvrant une modal.

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