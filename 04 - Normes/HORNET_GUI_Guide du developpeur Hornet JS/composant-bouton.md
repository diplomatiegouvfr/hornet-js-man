# Le composant Bouton

## Description 

Le bouton produit un élément de type submit.

![bouton](./sources/composants/basique/bouton.png)

## Utilisation

Il doit être initialisé avec une structure comme suit :

```javascript
var item = {
    type: "submit",
    id: "form_btnTest",
    name: "btnTest",
    value: "test value",
    onClick : function() {console.log("onClick")},
    className: "hornet-button",
    label: "test label",
    title: "title"
};
```

| attibut   | description                                                        |
| --------- | ------------------------------------------------------------------ |
| type      | le type input html submit, button, radio, checkbox, etc            |
| id        | identifiant unique du bouton                                       |
| name      | nom du bouton                                                      |
| value     | valeur correspondante à ce bouton pour la soumission du formulaire |
| onClick   | fonction exécutée sur le click                                     |
| className | style appliqué sur le bouton                                       |
| label     | texte sur du bouton                                                |
| title     | title sur du bouton                                                |
[HornetButton - attributs]

Les attibuts à renseigner sont `disabled` et `item`.

```javascript

import HornetButton = require("hornet-js-components/src/button/button");

<HornetButton 
    disabled={item.id + "_" + item.name} 
    item={item}
/>
```

### Autre cas d'utilisation

Les boutons peuvent être utilisés dans le formulaire `HornetForm` en renseignant l'attribut `buttons` comme une liste d'items vu ci-dessus.

Voir la documentation de [HornetForm](./Composants-formulaires.md).
