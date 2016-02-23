# React

## Mixin Hornet

Le decorator `HornetComponent.ApplyMixins()` permet d'ajouter les comportements du framework aux composants React. Cela va de l'`internationalisation`, à l'activation du mode `debounce`, ...

Documentation React sur les mixins:https://facebook.github.io/react/docs/reusable-components.html#mixins

La liste complète : 

- `FluxibleMixin` : accès aux informations de store dans les composants React
- `hornetContextMixin` : accès au context applicatif 
- `internationalisationMixin` : fourni les méthodes lié à l'`I18n`
- `themeMixin` : gère les thèmes applicatifs
- `contextPathMixin` : détermine le contexte applicatif, ex : getContextPath
- `throttleMixin` : permet de gérer du throttle mais aussi du debounce
- `mergePropsMixin` : permet de transférer des props d'un parent à un enfant fils via spread attribut

L'utilisation du décorator dans du code tsx.

```javascript
import HornetComponent = require("hornet-js-components/src/hornet-component");
@HornetComponent.ApplyMixins()
class ContactPage extends HornetComponent<any,any> {

...

}
```

### Le mixin throttleMixin

Mixin permettant de proposer des méthodes `throttle` et `debounce` sur la gestion des évènements afin de se protéger contre les actions répétées de l'utilisateur (double click, appui maintenu sur la touche entrée, etc...).

`Throttle` : limite le nombre d'appels successifs à une fonction en imposant un intervalle de temps minimum entre chaque appel.

Exemple d'utilisation : sur les boutons de pagination d'un tableau Hornet, permet de faire défiler les pages à un rythme raisonnable lorsque la touche entrée est maintenue enfoncée.

`Debounce` : déclenche un seul appel de fonction lorque le délai entre chaque appel est inférieur au minimum.
Exemple d'utilisation : sur un formulaire Hornet, permet de ne soumettre qu'une seule fois le formulaire lorsque la touche entrée reste enfoncée sur le bouton de validation. 

Voir la documentation de lodash pour des explications détaillées sur ces fonctions (https://lodash.com/docs#throttle).

Ce mixin expose une méthode `throttle` et une méthode `debounce` à chaque composant.

Il est préférable d'encapsuler la méthode à appeler dans la fonction `componentWillMount` plutôt qu'à chaque rendu.

Le mixin se charge d'annuler les appels potentiellement en attente lorsque le composant est démonté du DOM.

Il est possible de configurer le délai et même de le désactiver en passant au composant la propriété `throttleDelay` ou la propriété `debounceDelay` (désactivation si le délai est à 0). Le délai par défaut est de 500ms.

Exemple d'utilisation:

```javascript
@HornetComponent.ApplyMixins()
@HornetComponent.Error()
class maClass extends HornetComponent<PropTypesNs.ButtonProps, any> {

    @HornetComponent.AutoBind
    componentWillMount: function () {
        this.throttleOnSubmit = this.throttle(this.props.onSubmit);
    }


    @HornetComponent.AutoBind
    onChange: function (e) {
      e.preventDefault();
      this.throttleOnSubmit(this.state.data);
   }
}
```

## Decorator Error Hornet

React pas défaut ne catch pas bien les `throw` dans les méthodes de ses composants. Plutôt que de réaliser des appels explicites à "try catch" dans chaque méthode hérité de React, il est possible d'utiliser le decorateur `Error`. 

Il permet de réaliser les try catch dans les méthodes natives react suivantes : 

- `render`
- `componentWillMount`
- `componentDidMount`
- `componentWillReceiveProps`
- `shouldComponentUpdate`
- `omponentWillUpdate"`
- `componentDidUpdate`
- `componentWillUnmount`

L'utilisation du décorator `@HornetComponent.Error()` dans du code tsx.

```javascript	
import HornetComponent = require("hornet-js-components/src/hornet-component");
@HornetComponent.ApplyMixins()
@HornetComponent.Error()
class ContactPage extends HornetComponent<any,any> {
...
}
```