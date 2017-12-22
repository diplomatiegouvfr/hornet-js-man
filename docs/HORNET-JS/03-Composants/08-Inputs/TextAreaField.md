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
        <Form>
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
Les principaux attributs:

| attribut                | description                                                                   |
| ----------------------- | ------------------------------------------------------------------------------|
| id                      | Attribut id dans le DOM                                                       |
| label                   | Libelle du champ affiché                                                      |
| labelClass              | Nom de la classe CSS definit pour le label                                    |
| name                    | Nom de l'attribut dans le DOM                                                 |
| readOnly                | Empeche la saisie dans le champs (mode lecture seule)                         |
| row                     | Hauteur en nombre de lignes                                                   |
| maxLength               | Nombre maximum de caractère autorisé à la saisie                              |


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