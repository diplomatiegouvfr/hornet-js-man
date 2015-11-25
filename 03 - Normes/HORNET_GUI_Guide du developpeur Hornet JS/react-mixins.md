# Les mixins React

React permet l'utilisation de **mixins** afin de pouvoir proposer des comportements transverses aux composants.
Le framework Hornet en fourni un accessible par le require: 

```javascript
var HornetComponentMixin = require('hornet-js-core/src/mixins/react-mixins');
```
Documentation React sur les mixins:
[reusable-components#mixins](https://facebook.github.io/react/docs/reusable-components.html#mixins)


## Le mixin: throttleMixin
### Presentation
Ce mixin permet de proposer une méthode de throttle sur la gestion des évènements afin de se protéger contre les actions répétées de l'utilisateur (double click, appui sur la touche entrée, etc...).


Voir la documentation de lodash pour des explications sur la fonction utilisée: (https://lodash.com/docs#throttle).
 
Ce mixin expose une méthode `throttle` pour chaque composant. Il est nécessaire d'encapsuler la méthode à appeler avec cette méthode dans la fonction `componentWillMount`.
Le mixin se charge d'annuler les appels potentiellement en cours lorsque le composant est démonté du DOM.
 
Il est possible de configurer le délai (et même désactiver la fonction "throttle") en passant au composant la propriété "throttleDelay" (désactivation si le délai à est 0)
 
Exemple d'utilisation:


```javascript
React.createClass({
    componentWillMount: function () {
        this.throttleOnSubmit = this.throttle(this.props.onSubmit);
    },
 
    onChange: function (e) {
       e.preventDefault();
       this.throttleOnSubmit(this.state.data);
    }
})
```