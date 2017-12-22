# Le composant ButtonInfoAccessibilite

## Description

Composant informel sur les actions d'accessibilité clavier

![bouton](../sources/button/button-info-accessibilite.png)

Attributs du composant :

| Attribut             | Type                         | Description                                          |
| ---------------------|------------------------------| -----------------------------------------------------|
| message              | array[string]                | Messages internationalisés                           |
| shortcutDescriptions | ShortcutDescription          | Descriptions de raccourcis clavier                   |
| srcImg               | string                       | Source de l'image associé                            |



Attributs de l'objet shortcutDescriptions:

| Attribut     | Type             | Description                                                                                               |
|--------------|------------------|-----------------------------------------------------------------------------------------------------------|
| and          | boolean          | Lorsque shortcuts contient plusieurs éléments, indique si tous les éléments sont nécessaires
| description  | string           | Description de l'action correspondante                                                                     |
| or           | boolean          | Lorsque shortcuts contient plusieurs éléments, indique si l'un OU l'autre de ces éléments peut-être utilisé|
| shortcuts    | array[string]    | Représentation(s) de la ou des touches du clavier                                                          |


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