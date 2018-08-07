# Le composant TextAreaField

Champs de saisie d'un formulaire

## Description

Le composant `TextAreaField` est zone de saisie dans un formulaire. Il permet une saise muti-ligne d'information

![TextAreaField](../sources/form/textarea-field/textarea-field.png)

## Utilisation

```javascript

import { TextAreaField } from "hornet-js-react-components/src/widget/form/textarea-field";

render(): JSX.Element {
    return(
    <div>
        <Form id="form-example">
             <Row>
                <TextAreaField
                    name="commentaire"
                    label={"Zone de commentaire"}
                    maxLength={255}
                    rows={4}
                />
            </Row>
        </Form>
    </div>
    );
}

```

Tous les attributs standart HTML peuvent être utilisé

Le composant possède les propriétés des interfaces:

[HornetWrittableProps](/hornetshowroom/composant/page/hornet-js/composants/proprietes-hornet-component)

[HornetClickableProps](/hornetshowroom/composant/page/hornet-js/composants/proprietes-hornet-component)

[HornetBasicFormFieldProps](/hornetshowroom/composant/page/hornet-js/composants/proprietes-hornet-component)

Les principaux attributs:

| Attribut                | Description                                                                   | Obligatoire | Valeur par défaut | Type        |
| ----------------------- | ------------------------------------------------------------------------------|-------------|-------------------|-------------|
| resettable              | Définit si le champs peut être remis à zéro                                   | &nbsp;      | true              | boolean     |
| extendable              | Définit si la textarea se redimensionnera automatiquement                     | &nbsp;      | true              | boolean     |
| rows                    | Indique le nombre de ligne visible dans la zone de texte                      | &nbsp;      | 6                 | number      |

Pour associer au composant un compteur de caractères, les attributs suivants doivent être définis

| Attribut                | Description                                                                   | Obligatoire | Valeur par défaut | Type        |
| ----------------------- | ------------------------------------------------------------------------------|-------------|-------------------|-------------|
| displayCharNumber       | Définit si la textarea doit afficher le nombre de caractères entrés           | &nbsp;      | true              | boolean     |
| maxChar                 | Nombre maximum de caractères autorisé, la saisie sera toujours possible au dela contairement à l'attribut maxLength | &nbsp; | true | boolean |
| showAlert               | Définit si l'alerte indiquant que le nombre de caractères est trop élevé s'affichera | &nbsp;| true              | boolean     |
| alertMessage            | Message de l'alerte indiquant que le nombre de caractères est trop élevé       | &nbsp;      | &nbsp;            | string      |
| alertTitle              | Titre de l'alerte indiquant que le nombre de caractères est trop élevé         | &nbsp;      | &nbsp;            | string      |
| charLabel               | Label d'affichage du nombre de caractères                                      | &nbsp;      | &nbsp;            | string      |
| displayMaxCharInLabel   | Définit si le nombre de caractères maximum doit être ajouté au label          | Non         | false             | boolean     |


## Live coding

```javascript showroom

return  (
    <TextAreaField
        name="Commentaire"
        label={"Commentaire label"}
        labelClass="blocLabelUp"
        maxLength={255}
	    rows={4} />
 );
 
```