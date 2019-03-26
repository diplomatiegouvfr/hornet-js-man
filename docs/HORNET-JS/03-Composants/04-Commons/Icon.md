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

| Attribut                | Description                                                                             | Obligatoire | Valeur par défaut | Type|
| ----------------------- | ----------------------------------------------------------------------------------------|-------------|-------------------|-----|
| action                  | Fonction déclenchée lorsque le bouton correspondant à l'icône est pressé ou cliqué      | &nbsp;      | &nbsp;            |() => void|
| alt                     | Texte alternatif                                                                        | Oui         | &nbsp; | string |
| classImg                | Classe CSS de l'image                                                                   | &nbsp;      | &nbsp; | string |
| classLink               | Classe CSS du lien                                                                      | &nbsp;      | &nbsp; | string |
| idImg                   | Identifiant HTML de l'image rendue                                                      | &nbsp;      | &nbsp; | string |
| idLink                  | Identifiant HTML du lien                                                                | &nbsp;      | &nbsp; | string |
| url                     | Url de l'image                                                                          | &nbsp;      | "#" | string |
| src                     | Attribut id dans le DOM                                                                 | Oui         | &nbsp; | string |
| title                   | Texte d'infobulle du lien                                                               | Oui         | &nbsp; | string |
| tabIndex                | Valeur de l'attribut HTML tabIndex à affecter au lien ou bouton correspondant à l'icône | &nbsp;      | &nbsp; | number |
| target                  | Cible du lien                                                                           | &nbsp;      | &nbsp; | string |


## Aperçu des icônes existants

[Icônes](/hornetshowroom/pictogrammes)

## Live coding

```javascript showroom
return (
    <Icon src={Picto.black.editer} alt={"editer"} title={"editer"} />
 );
```