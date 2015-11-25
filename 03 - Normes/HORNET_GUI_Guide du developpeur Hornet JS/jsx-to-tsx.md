# Guide de passage de JSX vers TSX
- Renommer le fichier .jsx en .tsx
- Remplacer les 
```javascript
 var XXX = require(‘XXX’)
 ```
  par 
  ```javascript
 import XXX = require(‘XXX’)
 ```

- Ajouter en entête la ligne 
  ```javascript
///<reference path="xxxxxxxx/hornet-js-ts-typings/definition.d.ts" />
 ```
- Remplacer 
  ```javascript
module.exports = MyComponent
 ```
par
  ```javascript
export = MyComponent;
 ```
- Si le composant est simple et n’a pas besoin des mixins :
 - Transformer le composant en une classe qui étend le composant `React.Component`
 - Sinon transformer le composant en une classe qui étend le composant `HornetComponent` et ajouter le décorateur 
   ```javascript
@HornetComponent.ApplyMixins
 ```
  en entête de classe

- Créer un objet pour typer les `props`.
 - Cet objet doit être créé dans un fichier à part (`myComponent-props.ts`) afin de permettre 
un export directe du composant sans transformer le fichier en "namespace".
 - Cet objet doit étendre l'interface `React.Props` pour avoir automatiquement les clés `children, ref`, etc...
 - Importer ce fichier dans le composant
 - Utiliser ce typage dans le premier type paramétré de la classe étendue:


 ```javascript
import PropsNs = require("src/mycomponent/mycomponent-props");

@HornetComponent.ApplyMixins()
class MyComponent extends HornetComponent<PropsNs.MyComponentProps,any> {
    [...]
}
```

- Faire passer les traitements de `getInitialState` dans le constructeur:
  ```javascript
getInitialState: function () {
    return {
        state1Value: XXXX,
        [...]
    }
}
 ```
en
  ```javascript
constructor(props:MyComponentProps, context?:any) {
    super(props, context);
    
    this.state = {
       state1Value: XXXX,
        [...]
    };
}
 ```


- Transformer les `propTypes`:
  ```javascript
propTypes: {
        value: React.PropTypes.XXXXX,
        [...]
}
 ```
en
  ```javascript
 static propTypes = {
     value: React.PropTypes.XXXXX,
     [...]
 };
 ```
 
 
- Transformer les `defaultProps`:
```javascript
getDefaultProps: function () {
    return {
        value: XXXX,
        [...]
    }
},
```
en
```javascript
static defaultProps = {
    value: XXXX,
    [...]
};
```



- Transformer les branchements aux stores:
```javascript
statics: {
    storeListeners: [XXXXXX]
},
```
en
```javascript
static storeListeners = [XXXXXX];
```



- Transformer les autres fonctions du composant pour qu'elles soient écrites en TypeScript. Exemple:
```javascript
render: function () {
    return [...];
}
```
en
```javascript
render() {
    return [...];
}
```
  - Penser à les rendre `private` si besoin

- Appliquer le décorateur d'`AutoBinding` sur les méthodes ayant une référence à `this` et pouvant être appelées hors du 
contexte du composant (Exemple: méthodes répondant à des évènements React ou à des changements dans les stores).
```javascript
@HornetComponent.AutoBind
onChange(e:React.SyntheticEvent) {
    [...]
}
```