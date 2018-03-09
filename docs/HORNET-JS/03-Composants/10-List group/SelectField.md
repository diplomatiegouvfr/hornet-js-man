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

[HornetWrittableProps](/hornetshowroom/composant/page/hornet-js/composants/hornet-component-props)

[HornetClickableProps](/hornetshowroom/composant/page/hornet-js/composants/hornet-component-props)

[HornetBasicFormFieldProps](/hornetshowroom/composant/page/hornet-js/composants/hornet-component-props)

[HornetComponentDatasourceProps](/hornetshowroom/composant/page/hornet-js/composants/hornet-component-props)

[HornetComponentChoicesProps](/hornetshowroom/composant/page/hornet-js/composants/hornet-component-props)

Les principaux attributs:

| Attribut                | Description                     | Obligatoire | Valeur par défaut | Type |
| ----------------------- | --------------------------------|-----------|-----------|-----------|
| label                   | Libellé                         ||"blocLabelUp"|string|
| labelClass              | Classe du label                 |||string|
| dataSource              | Liste des options               |||Datasource|
| data                    | Liste des options sans passer par un datasource|||any|
| name                    | Nom du champs         |Oui||string|
| valueKey                | clé indiquant le champs de valeur des données ||"value"|string|
| labelKey                | clé indiquant le champs de libellé des données ||"label"|string|



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