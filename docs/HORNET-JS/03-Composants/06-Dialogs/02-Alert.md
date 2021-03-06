# Le composant Alert

## Description

Le composant `Alert` affiche un message avec une demande de confirmation par l'utilisateur (boutons `valider` et `annuler`).

![Boîte de confirmation](../sources/dialog/alert-only-valid.png)

Alert reprend le comportement et donc les principes du composant Modal.

## Utilisation

Nous retrouvons donc les attributs: `isVisible`, `onClickOk`, `onClickOutsideDialog` et `className`.

Dans l'exemple suivant, la popup affiche un formulaire et ses boutons.
 
```javascript
import { Alert } from "hornet-js-react-components/src/widget/dialog/alert";

/*Définition de la variable liée a l'alerte qui permettra d'appeler close() sur l'alert*/
private alert: Alert;

render(): JSX.Element {
    return (
        <div>
            ...
            <Alert
                message={"Etes-vous sûr(e) de vouloir supprimer ce secteur?"}
                isVisible={this.state.isOpenAlertDelete}
                onClickOk={this._supprimer}
                onClickClose={this.closeAlert}
                title={"Suppression de secteur"}
            />
            ...
        </div>
    );
}

/**
 * Méthode déclenchant la fermeture de l'alerte
 */
private closeAlert(): void {
    this.alert.close();
}
```

Les attributs du composant `Alert` :

| Attribut             | Description                                                                    | Obligatoire | Valeur par défaut | Type       |
| -------------------- | ------------------------------------------------------------------------------ | ----------- |------------------ |----------- |
| message              | Message affiché dans la popup                                                  | Oui         | &nbsp;            | string     |
| isVisible            | Rendre le composant visible                                                    | &nbsp;      | false             | boolean    |
| onClickOk            | Passer la méthode à appeler lorsque l'utilisateur clique sur le bouton `Valider` | &nbsp; | &nbsp; | React.MouseEventHandler<HTMLInputElement> |
| onClickClose         | Passer la méthode à appeler lorsque l'utilisateur clique sur la croix (en haut à droite) ou sur le bouton `Annuler` | &nbsp; | &nbsp; | React.MouseEventHandler<HTMLInputElement> |
| title                | Titre de l'alerte                                                              | &nbsp;      | &nbsp;            | string     |
| valid                | Fonction de validation                                                         | &nbsp;      | &nbsp;            | string     |
| validTitle           | Title du bouton de validation                                                  | &nbsp;      | &nbsp;            | string     |
| underlayClickExits   | Permet de sortir de l'alerte lors d'un clique en dehors de celle-ci            | &nbsp;      | false             | boolean    |
| escapeKeyExits       | Permet de spécifier si on peut utiliser la touche "Echappe" pour sortir de l'alerte | &nbsp; | false             | boolean    |
| notificationId       | Identifiant des notifications                                                  | &nbsp;      | &nbsp;            | string     |
| dialogId             | identifiant de l'alerte                                                        | &nbsp;      | &nbsp;            | string     |

## Live coding

`Attention : mettre l'option isVisible à true pour voir l'alerte`

```javascript showroom

    /*Définition de la variable liée a l'alerte qui permettra d'appeler close() sur l'alert*/
        private alert: Alert;
        return (
            <div>
                <Alert
                    message={"Etes-vous sûr(e) de vouloir supprimer ce secteur?"}
                    isVisible={false}
                    onClickOk={function() {alert("onClickOk")}}
                    title={"Suppression de secteur"}
                />
            </div>
        );
}
```