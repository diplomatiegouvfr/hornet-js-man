# Le composant Icon

## Description

Le composant permet d'afficher un icône.

## Utilisation

```javascript

 import { Icon } from "hornet-js-react-components/src/widget/icon/icon";

render(): JSX.Element {
    return(
    <div>
        <Icon src="test" alt="" title="" />
    </div>
    );
}
```


Attributs du composant :

| attribut                | description                                                                             |
| ----------------------- | ----------------------------------------------------------------------------------------|
| action                  | Fonction déclenchée lorsque le bouton correspondant à l'icône est pressé ou cliqué      |
| alt                     | Texte alternatif                                                                        |
| classImg                | Classe CSS de l'image                                                                   |
| classLink               | Classe CSS du lien                                                                      |
| idImg                   | Identifiant HTML de l'image rendue                                                      |
| idLink                  | Identifiant HTML du lien                                                                |
| url                     | Url de l'image                                                                          |
| src                     | Attribut id dans le DOM                                                                 |
| title                   | Texte d'infobulle du lien                                                               |
| tabIndex                | Valeur de l'attribut HTML tabIndex à affecter au lien ou bouton correspondant à l'icône |
| target                  | Cible du lien                                                                           |
| hasPopUp                | Indicateur d'ouverture d'un popup suite à clic sur bouton                               |


## Aperçu des icônes existants

[Icônes](/hornetshowroom/pictogrammes)

## Live coding

```javascript showroom
return (
    <Icon src={Picto.black.editer} alt={"editer"} title={"editer"} />
 );
```