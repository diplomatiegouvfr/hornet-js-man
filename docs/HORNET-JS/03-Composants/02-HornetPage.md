# Le composant HornetPage

## Description

Le composant `HornetPage` est un composant de "haut-niveau", il correspond à une page de l'application. Il hérite de `HornetComponent`.

### Propriétés React du composant

|        Attribut           | Description                                                                                | Obligatoire | Valeur par défaut | Type |
| ------------------------- | ------------------------------------------------------------------------------------------ |-------------|-------------|-------------|
| classNameExpanded         | Nom du className a utiliser pour les layout sensitive components (composant qui s'étendent)|||string|
| currentWorkingZoneWidth   | Largeur maximale de la zone de travail en pixel utilisée                                   |||string|
| navigateData              | Données transmises par la page précédente                                                  |||any|
| workingZoneWidth          | Largeur maximale de la zone de travail en pixel                                            |||string|
| hasError                  | Définit si le composant contient des erreurs                                               |||boolean|
| error                     | Erreurs du composant                                                                       |||any|

### Méthodes / attributs

#### prepareClient

Cette méthode permet d'effectuer les appels d'API et initialisations éventuellement nécessaires.
Elle sera appelée au moment où le composant Page sera monté (au sens  React), à l'exécution de la méthode: `componentDidMount` (cf. cycle de vie React)
uniquement côté client.

#### getService

Cette méthode renvoie le service de la page préalablement associé à cette dernière lors de la déclaration des `routes` lors de l'instanciation de 
la class `PageRouteInfos`

#### navigateTo

Cette méthode permet de changer d'url en passant des données à la page suivante:


```javascript

this.navigateTo(URL, {},() =>{
    callback();
});

```

##### Fonctionnement: 

Permet de déclencher l'évènement `CHANGE_URL_WITH_DATA_EVENT`  avec les data transmises, et demander l'affichage du composant correspondant. 
Ces data pourront être récupérées dans le composant appelé via `this.props.navigateData`.


#### Récupération des attributs d'url

Les paramètres d'Url peuvent être récupérés via l'attribut `attributes`

exemple: 

Lors de la déclaration de la route d'une page:

``` javascript

        this.addPageRoute("/(\\w+)/(\\d+)",
            (mode, id) => new PageRouteInfos(FichePartenairePage, {mode: mode, id: id}, FichePartenaireServiceImpl),
            Roles.ADMIN
        );
        
```

Si la page `partenaires/editer/26` est demandée, l'attribut `attributes` de la class `HornetPage` sera formé tel que:
 
``` javascript

attributes = {
        mode: "editer",
        id: 26
};

```

## Création d'une HornetPage

Code de base de création d'une HornetPage :

```javascript
import { HornetPage, HornetPageProps } from "hornet-js-react-components/src/widget/component/hornet-page";

export class MaPage extends HornetPage<MonService, MaPageProps, any> {
    ...
}
```