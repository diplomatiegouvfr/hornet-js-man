# Le composant ButtonInfoAccessibilite

## Description

Composant informel sur les actions d'accessibilité clavier

![bouton](../sources/button/button-info-accessibilite.png)

Attributs du composant :

| Attribut             |Description                                          | Obligatoire | Valeur par défaut | Type             |
| ---------------------|-----------------------------------------------------| ------------|-------------------|------------------|
| message              | Messages internationalisés                          |             | ""                | any              |
| shortcutDescriptions | Descriptions de raccourcis clavier                  |             | []                |array[ShortcutDescription]          |
| srcImg               | Source de l'image associé                           |             |                   |string                       |



Attributs de l'objet shortcutDescriptions:

| Attribut     | Description                                                                                  | Obligatoire |Valeur par défaut | Type |
|--------------|----------------------------------------------------------------------------------------------|-------------| -----|-----|
| and          | Lorsque shortcuts contient plusieurs éléments, indique si tous les éléments sont nécessaires |   | | boolean |
| description  | Description de l'action correspondante                                                       | Oui | |string |
| or           | Lorsque shortcuts contient plusieurs éléments, indique si l'un OU l'autre de ces éléments peut-être utilisé| |  |boolean |
| shortcuts    | Représentation(s) de la ou des touches du clavier                                                          | Oui |  |array[string] |


## Utilisation
Il doit être initialisé avec une structure comme suit :

```javascript

import {ButtonInfoAccessibilite} from "src/widget/button/button-info-accessibilite";

let shortcutDescriptions = [
    {
      shortcuts: ["Enter"],
      or: true,
      description: "active l'element sur lequel le focus est positionné"
    }
 ];

<ButtonInfoAccessibilite
    message={"un message"}
    shortcutDescriptions ={shortcutDescriptions}
   />

```


## Live coding

```javascript showroom

     let shortcutDescriptions = [
          {
              shortcuts: ["Enter", "Space"],
              or: true,
              description: HornetComponent.getI18n("menu.shortcuts.enterSpace")
          },
          {
              shortcuts: ["Echap"],
              description: HornetComponent.getI18n("menu.shortcuts.escape")
          },
          {
              shortcuts: ["←"],
              description: HornetComponent.getI18n("menu.shortcuts.arrowLeft")
          }
     ];

    return  <ButtonInfoAccessibilite
                message={"un message"}
                shortcutDescriptions ={shortcutDescriptions}
            />
```