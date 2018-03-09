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
| action                  | Fonction déclenchée lorsque le bouton correspondant à l'icône est pressé ou cliqué      |             ||() => void|
| alt                     | Texte alternatif                                                                        |Oui            ||string|
| classImg                | Classe CSS de l'image                                                                   |             ||string|
| classLink               | Classe CSS du lien                                                                      |             ||string|
| idImg                   | Identifiant HTML de l'image rendue                                                      |             ||string|
| idLink                  | Identifiant HTML du lien                                                                |             ||string|
| url                     | Url de l'image                                                                          |             |"#"|string|
| src                     | Attribut id dans le DOM                                                                 |Oui            ||string|
| title                   | Texte d'infobulle du lien                                                               |Oui            ||string|
| tabIndex                | Valeur de l'attribut HTML tabIndex à affecter au lien ou bouton correspondant à l'icône |             ||number|
| target                  | Cible du lien                                                                           |             ||string|
| hasPopUp                | Indicateur d'ouverture d'un popup suite à clic sur bouton                               |             ||boolean|


## Aperçu des icônes existants

[Icônes](/hornetshowroom/pictogrammes)

## Live coding

```javascript showroom
return (
    <Icon src={Picto.black.editer} alt={"editer"} title={"editer"} />
 );
```