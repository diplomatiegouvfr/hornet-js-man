# Le composant InputField

## Description

Champs de saisie d'un formulaire

![InputField](../sources/form/input-field/input-field.png)

## Utilisation

```javascript

 import { InputFileField } from "hornet-js-react-components/src/widget/form/input-field";

render(): JSX.Element {
    return(
    <div>
        <Form id="form-example">
            <Row>
                <InputField name="nom" />
            </Row>
        </Form>
    </div>
    );
}

```

Tous les attributs standart HTML peuvent être utilisés

Le composant possède les propriétés des interfaces:

[HornetWrittableProps](/hornetshowroom/composant/page/hornet-js/composants/proprietes-hornet-component)

[HornetClickableProps](/hornetshowroom/composant/page/hornet-js/composants/proprietes-hornet-component)

[HornetBasicFormFieldProps](/hornetshowroom/composant/page/hornet-js/composants/proprietes-hornet-component)

[ReactFocusDOMAttributes](/hornetshowroom/composant/page/hornet-js/composants/proprietes-hornet-component)

Les principaux attributs:

| Attribut                | Description                                                                   | Obligatoire | Valeur par défaut | Type    |
| ----------------------- | ------------------------------------------------------------------------------|-------------|-------------------|---------|
| id                      | Attribut id dans le DOM                                                       | &nbsp;      | &nbsp;            | string  |
| label                   | Libelle du champ affiché                                                      | &nbsp;      | &nbsp;            | string  |
| name                    | Nom de l'attribut dans le DOM                                                 | &nbsp;      | &nbsp;            | string  |
| required                | Définit si le champ est obligatoire                                           | &nbsp;      | &nbsp;            | boolean |
| requiredLabel           | Libelle du label affiche lorsqu'on se positionne sur l'astérisque             | &nbsp;      | &nbsp;            | string  |
| maxLength               | Nombre maximum de caractère autorisé à la saisie                              | &nbsp;      | &nbsp;            | number  |
| resettable              | Par défaut à true. Détermine la présence du bouton `reset` dans le champ      | &nbsp;      | &nbsp;            | boolean |

Pour associer au composant un compteur de caractères, les attributs suivants doivent être définis

| Attribut                | Description                                                                   | Obligatoire | Valeur par défaut | Type |
| ----------------------- | ------------------------------------------------------------------------------|-------------|-------------------|------|
| displayCharNumber       | Définit si l'input doit afficher le nombre de caractères saisis               |Non          |true               |boolean|
| displayMaxCharInLabel   | Définit si le nombre de caractères maximum doit être ajouté au label          |Non          |false              |boolean|
| maxChar                 | Nombre maximum de caractères autorisé, la saisie sera toujours possible au dela contairement à l'attribut maxLength|Non|true|boolean|
| showAlert               | Définit si l'alerte indiquant que le nombre de caractères est trop élevé s'affichera |Non    |true               |boolean|
| alertMessage            | Message de l'alerte indiquant que le nombre de caractères est trop élevé       |Non          |&nbsp;             |string|
| alertTitle              | Titre de l'alerte indiquant que le nombre de caractères est trop élevé         |Non          |&nbsp;             |string|
| charLabel               | Label d'affichage du nombre de caractères                                     |Non          |&nbsp;             |string|

## Live coding

```javascript showroom

return (
  	<row>
        <InputField
            name="rue"
            id="test1"
            label="Rue"
            required
            displayCharNumber={true}
            displayMaxCharInLabel={true}
            maxChar={50}
            showAlert={true}/>
        <InputField
            name="complrue"
            label="Complement de rue" />
        <InputField
            name="cp"
            label={"Code postal"}
            maxLength={5}/>
        <InputField
            name="exemple"
            label="Se positionner sur l'astérisque"
            required
            requiredLabel="je suis le message" />
	</row>
 );
 
```