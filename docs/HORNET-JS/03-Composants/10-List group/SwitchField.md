# Le composant SwitchField

## Description

Le composant `SwitchField` permet de proposer une liste de choix à l'utilisateur.

## Utilisation

```javascript

import { SwitchField } from "hornet-js-react-components/src/widget/form/switch-field";

render(): JSX.Element {

    let data = [{value: 1, label: "lundi"},
    {value:2, label:"mardi"},
    {value: 3, label: "mercredi"},
    {value:4, label:"jeudi"},
    {value:5, label:"Vendredi"},
    {value:6, label:"Samedi"},
    {value:7, label:"Dimanche"}];

    return(
    <div>
        <Form id="form-example">
             <Row>
                 <SwitchField name="switch"
                    label={"Switch"}
                    datas={data} />
            </Row>
        </Form>
    </div>
    );
}

```


Attributs du switchfield:

| Attribut                | Description                                    | Obligatoire | Valeur par défaut | Type      |
| ----------------------- | -----------------------------------------------|-------------|-------------------|-----------|
| label                   | Libellé                                        | &nbsp;      | "blocLabelUp"     | string    |
| labelClassName          | Classe du label                                | &nbsp;      | &nbsp;            | string    |
| inputClassName          | Classe des inputs                              | &nbsp;      | &nbsp;            | string    |
| datas                   | Liste des options                              | &nbsp;      | &nbsp;            | any       |
| name                    | Nom du champs                                  | Oui         | &nbsp;            | string    |
| valueKey                | clé indiquant le champ de valeur des données  | &nbsp;      | "value"           | string    |
| labelKey                | clé indiquant le champ de libellé des données | &nbsp;      | "label"           | string    |
| defaultValue            | Valeur par défaut                             | &nbsp;      | &nbsp;            | any    |
| onClick                 | méthode appelée au click à la souris et sur une touche  | &nbsp;      | &nbsp;            | MouseEventHandler<HTMLElement>    |
| onKeyDown               | méthode appelée au click sur une touche       | &nbsp;      | &nbsp;            | KeyboardEventHandler<HTMLElement>    |
| onChange                | méthode appelée lors d'un changement de valeur| &nbsp;      | &nbsp;            | FormEventHandler<HTMLElement>    |
| shouldRadioBeChecked    | méthode appelée lors d'un render pour check option | &nbsp;      | &nbsp;            | Function |


## Live coding

```javascript showroom

let data = [{value: 1, label: "lundi"},
    {value:2, label:"mardi"},
    {value: 3, label: "mercredi"},
    {value:4, label:"jeudi"},
    {value:5, label:"Vendredi"},
    {value:6, label:"Samedi"},
    {value:7, label:"Dimanche"}];


return  (
      <div>
        <Form id="form-example">
             <Row>
                 <SwitchField name="switch"
                    label={"Switch"}
                    datas={data} />
            </Row>
        </Form>
    </div>
 );
```