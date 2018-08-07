# Le composant SelectField

Ce composant est deprecated ! Il ne doit être utilisé que pour un besoin mobile, sinon utiliser AutocompleteField.

Liste de choix d'un formulaire

## Description

Le composant `SelectField` permet de proposer une liste de choix à l'utilisateur.

![bouton](../sources/form/select-field/select-field.png)


## Utilisation

```javascript

import { InputFileField } from "hornet-js-react-components/src/widget/form/input-field";

render(): JSX.Element {
    return(
    <div>
        <Form id="form-example">
             <Row>
                 <SelectField name="criteres.idSecteur"
                    label={intlMessages.fields.criteres.idSecteur.label}
                    dataSource={this.dataSourceSecteurs} />
            </Row>
        </Form>
    </div>
    );
}

```

Tous les attributs standards de la balise html select peuvent être utilisés.

Le composant possède les propriétés des interfaces:

[HornetWrittableProps](/hornetshowroom/composant/page/hornet-js/composants/proprietes-hornet-component)

[HornetClickableProps](/hornetshowroom/composant/page/hornet-js/composants/proprietes-hornet-component)

[HornetBasicFormFieldProps](/hornetshowroom/composant/page/hornet-js/composants/proprietes-hornet-component)

[HornetComponentDatasourceProps](/hornetshowroom/composant/page/hornet-js/composants/proprietes-hornet-component)

[HornetComponentChoicesProps](/hornetshowroom/composant/page/hornet-js/composants/proprietes-hornet-component)

Les principaux attributs:

| Attribut                | Description                                    | Obligatoire | Valeur par défaut | Type      |
| ----------------------- | -----------------------------------------------|-------------|-------------------|-----------|
| label                   | Libellé                                        | &nbsp;      | "blocLabelUp"     | string    |
| labelClass              | Classe du label                                | &nbsp;      | &nbsp;            | string    |
| dataSource              | Liste des options                              | &nbsp;      | &nbsp;            | Datasource|
| data                    | Liste des options sans passer par un datasource| &nbsp;      | &nbsp;            | any       |
| name                    | Nom du champs                                  | Oui         | &nbsp;            | string    |
| valueKey                | clé indiquant le champ de valeur des données  | &nbsp;      | "value"           | string    |
| labelKey                | clé indiquant le champ de libellé des données | &nbsp;      | "label"           | string    |
| nullable                | Indique si la valeur par défaut peut être à null | &nbsp;      | false             | boolean   |


## Live coding

```javascript showroom

let options = [
       {id: 1, libelle: "option 1"},
       {id: 2, libelle: "option 2"},
       {id: 3, libelle: "option 3"},
       {id: 4, libelle: "option 4"},
   ];

let dataSourceOption = new DataSource<any>(options, {value: "id", label: "libelle"});

return  (
       <SelectField
          name= {"optionlist"}
          label= {"liste d'option"}
          dataSource= {dataSourceOption}
      />
 );
```