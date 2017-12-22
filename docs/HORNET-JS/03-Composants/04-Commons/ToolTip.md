# Le composant ToolTip

## Description

Le composant `ToolTip` affiche une icône d'information qui laisse apparaître une infobulle au survol du pointeur.

![infobulle](../sources/tool-tip/tool-tip.png)

## Utilisation

| attribut   | description                                         | valeur par défaut                                |
| ---------- | --------------------------------------------------- | ------------------------------------------------ |
| alt        | Propriété alt de l'image                            |                                                  |
| classImg   | Style appliqué sur l'élément 'img'                  | imgTooltip                                       |
| classSpan  | Style appliqué sur l'élément 'span'                 | tooltip                                          |
| icoToolTip | Url de l'image dans le théme                        | /img/tooltip/ico_tooltip.png                     |
| idImg      | Id de l'élément 'img'                               |                                                  |
| idSpan     | Id de l'élément 'span'                              |                                                  |
| src        | Url de l'image à afficher                           | this.genUrlTheme("/img/tooltip/ico_tooltip.png") |

```javascript

import ToolTip = require("hornet-js-components/src/tool-tip/tool-tip");

<ToolTip idSpan="exemple-tooltip" alt="Texte infobulle" />

```

Le composant infobulle peut être activé sur un champ de formulaire lorsque la balise `tooltip` est renseignée.Le positionnement de l'infobulle est en haut de l'icône 'i' placé à coté du champ.

```javascript

<Field name="Test" tooltip="Texte infobulle" />

```

## Live coding

```javascript showroom

    return (
        <ToolTip idSpan="exemple-tooltip" alt="Texte infobulle" />
    );
```