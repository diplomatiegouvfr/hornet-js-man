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
        <Form>
            <Row>
                <InputField name="nom" />
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
| name                    | Nom de l'attribut dans le DOM                                                 |
| required                | Définit si le champ est obligatoire                                           |
| requiredLabel           | Libelle du label affiche lorsqu'on se positionne sur l'astérisque             |
| maxLength               | Nombre maximum de caractère autorisé à la saisie                              |
| maxLength               | Nombre maximum de caractère autorisé à la saisie                              |
| resettable              | Par défaut à true. Détermine la présence du bouton `reset` dans le champ      |

## Live coding

```javascript showroom
return (
  	<row>
		<InputField name="rue" id="test1" label="Rue" required />
		<InputField name="complrue" label="Complement de rue" />
	    <InputField name="cp" label={"Code postal"} maxLength={5}/>
        <InputField name="exemple" label="Se positionner sur l'astérix" required requiredLabel="je suis le message" />
	</row>
 );
```