# Le composant Alerte

## Description

Le composant `HornetAlert` affiche un message avec une demande de confirmation par l'utilisateur (boutons `valider` et `annuler`).

![Boîte de confirmation](./sources/composants/dialog/boite-confirmation.png)

HornetAlert reprend le comportement et donc les principes de HornetModal.

## Utilisation

Nous retrouvons donc les attributs: `isVisible`, `onClickClose`, `onClickOutsideDialog` et `className`.

Dans l'exemple suivant, la popup affiche un formulaire et ses boutons.
 
```javascript
import HornetAlert = require("hornet-js-components/src/dialog/alert");

<HornetAlert message={intlMessages.confirmationSuppression} 
			 isVisible={this.state.isOpenAlertDelete}
             onClickOk={this._supprimer} 
         	 onClickCancel={this._closeMessageSupprimer} 
         	 onClickClose={this._closeMessageSupprimer}
             title={intlMessages.title}/>
```

Les attributs du composant `HornetAlert` :

| attribut | obligatoire | description |
| -------- | ----------- | ----------- |
| message| X | Message affiché dans la popup |
| isVisible| X | Rendre le composant visible |
| onClickOk| | Passer la méthode à appeler lorsque l'utilisateur clique sur le bouton `Valider`|
| onClickCancel | | Passer la méthode à appeler lorsque l'utilisateur clique sur le bouton `Annuler`|
| onClickClose | | Passer la méthode à appeler lorsque l'utilisateur clique sur la croix (en haut à droite) ou sur le bouton `Annuler`  |
| title | | Titre de l'alerte |
| valid | | Fonction de validation |
| cancel | | Fonction appelé lors de l'annulation de la modale |
| validTitle | | Title du bouton de validation |
| cancelTitle | | Title du bouton d'annulation |
| underlayClickExits | | Permet de sortir de l'alerte lors d'un clique en dehors de celle-ci |
| escapeKeyExits | | Permet de spécifier si on peut utiliser la touche "Echappe" pour sortir de l'alerte |
