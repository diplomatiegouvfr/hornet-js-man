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
| className | Style appliqué sur le bouton                                       |             |                   | string     |
| disabled  | Bouton désactivé ou non                                            |             | false             | boolean     |
| hasPopUp  | [Accessibilité] Indicateur si le bouton ouvre un popup             |             |                   | boolean     |
| id        | Identifiant unique du bouton                                       |             |                   | string     |
| label     | Texte sur du bouton                                                |             |                   | string     |
| name      | Nom du bouton                                                      |             |                   | string     |
| onClick   | Fonction exécutée sur le click                                     |             |                   | React.MouseEventHandler<HTMLElement>     |
| title     | Title sur du bouton                                                |             |                   | string     |
| type      | Le type input html submit, button, radio, checkbox, etc            |             |                   | string     |
| value     | Valeur correspondante à ce bouton pour la soumission du formulaire |             |                   | string     |
| url       | Url sur laquelle redirige le bouton                                |             |                   | string     |
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