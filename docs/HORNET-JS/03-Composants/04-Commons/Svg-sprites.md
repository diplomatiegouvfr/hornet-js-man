# Le composant SvgSprites

## Description

Le composant permet d'afficher d'afficher une icone en svg.

## Utilisation

```javascript

 import { SvgSprites } from "hornet-js-react-components/src/widget/icon/svg-sprites";

render(): JSX.Element {
    return(
        <SvgSprites icon="info" color="#757575" />
    );
}
```


Attributs du composant :

| Attribut                | Description                                                                             | Obligatoire | Valeur par défaut | Type|
| ----------------------- | ----------------------------------------------------------------------------------------|-------------|-------------------|-----|
| icon                | nom du svg contenu dans le sprite                                                                   | Oui      | &nbsp; | string |
| width               | largeur du svg                                                                      | &nbsp;      | "1.7em" | string |
| height                   | hauteur du svg                                                      | &nbsp;      | "1.7em" | string |
| url                  | lien vers le sprite                                                                | &nbsp;      | "/img/sprites.svg" | string |
| color                     | couleur de l'icone                                                                          | &nbsp;      | "#FFF" | string |
| classAdded                     | nom de classe supplémentaire                                                                 | &nbsp;         | &nbsp; | string |
| ariaLabel                     | Title du svg pour l'accessibilité                                                             | &nbsp;      | Icon name | string |


## Logique de sprite
Le composant `SvgSprites` récupère dans un sprite (un ensemble d'icônes contenus dans une seule grande image) une icône précise. L'`url` pointe vers ce sprite et `icon` récupère l'emplacement précis du svg voulu. Ce qui a pour résultat, dans le code, d'avoir (inclu dans une balise `<svg>`) l'appel à `<use xlinkHref="monsprite.svg#monIcone" />`.

### Détail du composant `SvgSprites`

```javascript
    return (
        <svg
            height={this.props.height}
            width={this.props.width}
            viewBox='0 0 24 24'
            className={`icon ico-${this.props.icon} ${ this.props.classAdded ? this.props.classAdded : "" }`}
            fill={this.props.color}
            aria-hidden="true"
            aria-label={this.props.icon}>
                <title>{this.props.icon}</title>
                <use xlinkHref={`${urlSprite}#ico-${this.props.icon}`} />
        </svg>
    );
```

### Liste des icônes disponibles dans le sprite

* account
* add
* carretDown
* bell
* cancel
* close
* calendar
* ratio
* ratioOpen
* error
* warning
* validate
* previous
* next
* tooltip
* arrowUpward
* arrowDownward
* list
* sort
* cog
* edit
* delete
* quickEdit
* registerEdit
* cancelEdit
* csv
* pdf
* odt
* ods
* consult
* user
* info

## Live coding

Pour récupérer le bon sprite il suffit de passer dans la props `icon` le nom de l'icône ci-dessus.

```javascript showroom
return (
    <SvgSprites icon="close" />
 );
```