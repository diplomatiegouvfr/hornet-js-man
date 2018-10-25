# Le composant Confirm

## Description

Le composant `Confirm` affiche un message avec une demande de confirmation par l'utilisateur (boutons `valider` et `annuler`).

![Boîte de confirmation](../sources/dialog/alert.png)

`Confirm` reprend le comportement et donc les principes du composant `Modal`.

## Utilisation

Nous retrouvons donc les attributs: `isVisible`, `onClickOk`, `onClickClose`,  et `className`.

Dans l'exemple suivant, la popup affiche un formulaire et ses boutons.
 
```javascript
import { Confirm } from "hornet-js-react-components/src/widget/dialog/confirm";

/*Définition de la variable liée au confirm qui permettra d'appeler close() sur le confirm*/
private confirm: Confirm;

render(): JSX.Element {
    return (
        <div>
            ...
            <Confirm
                message={"Etes-vous sûr(e) de vouloir supprimer ce secteur?"}
                isVisible={this.state.isOpenConfirmDelete}
                onClickOk={this._supprimer}
                onClickCancel={this.closeConfirm}
                onClickClose={this.closeConfirm}
                title={"Suppression de secteur"}
            />
            ...
        </div>
    );
}

/**
 * Méthode déclenchant la fermeture du confirm
 */
private closeConfirm(): void {
    this.confirm.close();
}
```

Les attributs du composant `confirm` :

| Attribut             | Description                                                                    | Obligatoire | Valeur par défaut | Type       |
| -------------------- | ------------------------------------------------------------------------------ | ----------- |------------------ |----------- |
| message              | Message affiché dans la popup                                                  | Oui         | &nbsp;            | string     |
| isVisible            | Rendre le composant visible                                                    | &nbsp;      | false             | boolean    |
| onClickOk            | Passer la méthode à appeler lorsque l'utilisateur clique sur le bouton `Valider` | &nbsp; | &nbsp; | React.MouseEventHandler<HTMLInputElement> |
| onClickClose         | Passer la méthode à appeler lorsque l'utilisateur clique sur la croix (en haut à droite) ou sur le bouton `Annuler` | &nbsp; | &nbsp; | React.MouseEventHandler<HTMLInputElement> |
| onClickCancel        | Passer la méthode à appeler lorsque l'utilisateur clique sur le bouton `Cancel` | &nbsp; | &nbsp; | React.MouseEventHandler<HTMLInputElement> |
| title                | Titre de la popup                                                              | &nbsp;      | &nbsp;            | string     |
| valid                | Fonction de validation                                                         | &nbsp;      | &nbsp;            | string     |
| cancel               | Fonction appelé lors de l'annulation de la modale                              | &nbsp;      | &nbsp;            | string     |
| validTitle           | Title du bouton de validation                                                  | &nbsp;      | &nbsp;            | string     |
| cancelTitle          | Title du bouton d'annulation                                                   | &nbsp;      | &nbsp;            | string     |
| underlayClickExits   | Permet de sortir du confirm lors d'un clique en dehors de celle-ci            | &nbsp;      | false             | boolean    |
| escapeKeyExits       | Permet de spécifier si on peut utiliser la touche "Echappe" pour sortir du confirm | &nbsp; | false             | boolean    |
| notificationId       | Identifiant des notifications                                                  | &nbsp;      | &nbsp;            | string     |
| dialogId             | identifiant du confirm                                                        | &nbsp;      | &nbsp;            | string     |
### Alerte sur une action d'un tableau

Les composants `Table` possèdent des colonnes d'action `ActionColumn` qui permettent d'effectuer des actions sur les éléments du tableau.
Il est possible d'afficher un confirm avant d'effectuer cette action.

Exemple:

```
render(): JSX.Element {
    return(
        <div>
             <Table>
                ...
                <Columns>
                    ...
                    <ActionColumn
                        ...
                        messageAlert={"Etes-vous sûr(e) de vouloir supprimer ce secteur?"}
                        titleAlert={"Suppression de secteur"}
                    />
                </Columns>
             </Table>
        </div>
    );
}
```

## Live coding

`Attention : mettre l'option isVisible à true pour voir le confirm`

```javascript showroom

    /*Définition de la variable liée au confirm qui permettra d'appeler close() sur le confirm*/
        private confirm: Confirm;
        return (
            <div>
                <Confirm
                    message={"Etes-vous sûr(e) de vouloir supprimer ce secteur?"}
                    isVisible={false}
                    onClickOk={function() {alert("onClickOk")}}
                    onClickCancel={function() {alert("onClickCancel")}}
                    title={"Suppression de secteur"}
                />
            </div>
        );
}
```