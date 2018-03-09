# Le composant LayoutSwitcher

## Description

![LayoutSwitcher](../sources/header/layout-switcher/layout-switcher.png)

Le composant `LayoutSwitcher` permet d'étendre l'espace de travail à la largeur disponible de l'écran.


## Utilisation

Aucun attribut obligatoire.
 
```javascript
import {LayoutSwitcher} from "hornet-js-react-components/src/widget/screen/layout-switcher";

<LayoutSwitcher />
```

## Composants impactés par l'expand

Certains composants peuvent s'étendre grâce à cette fonctionnalité :

- HornetPage
- FooterPage
- Menu

Ces composants possèdent une props workingZoneWidth à valoriser par la largeur que doit prendre le composant lorsque l'écran n'est pas étendu.
```javascript
    export class HornetApp extends HornetPage<any,HornetAppProps, any> {

        constructor(props: HornetAppProps, context?: any) {
            super(props, context);
                ...
        }

        static defaultProps = {
            workingZoneWidth: "1200px"
        };

            ...
    }
```

```javascript
    <Menu showIconInfo={true} workingZoneWidth={this.state.currentWorkingZoneWidth}/>

    <FooterPage workingZoneWidth={this.state.currentWorkingZoneWidth}>
    </FooterPage
```

Tous les éléments HTML sensibles à l'expansion de la zone de travail doivent posséder le style mainLayoutClassName et un style maxWidth lorsqu'il n'est pas étendu ou mainLayoutClassNameExpanded lorsqu'il est étendu
L'exemple ci-dessous est tiré d'un composant HornetPage qui possède un state classNameExpanded qui applique le bon style de class, et un state currentWorkingZoneWidth qui porte la bonne taille maximale définie dans le composant page.
```javascript
    <div id="header-expanded-zone" className={"inside " + this.state.classNameExpanded}
         style={{maxWidth: this.state.currentWorkingZoneWidth}}>
```

Les attributs du composant `LayoutSwitcher` :

| Attribut | Description | Obligatoire | Valeur par défaut | Type |
| -------- | ----------- | ----------- | ----------- | ----------- |
| switchIcon | Path vers l'icon à afficher pour le switcher. Par défaut on utilise l'icône ic_screen_ratio embarqué dans le thême hornet | |LayoutSwitcher.genUrlTheme("/img/header/ic_screen_ratio.svg")|string|
| switchTitle | Label affiché pour le bouton du layout. Par défaut on utilise la ressource `header.expand`  | ||string|
|id | Identifiant du composant | ||string|

## Live coding

`Attention: pour le live coding, le composant LayoutSwitcher doit avoir un identifiant "ls-exemple" `

```javascript showroom

	return (
            <LayoutSwitcher id="ls-exemple" />
    );
```