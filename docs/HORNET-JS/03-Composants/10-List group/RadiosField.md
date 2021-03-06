# Le composant RadiosField

Manuel d'utilisation du composant `RadiosField` Hornet.

## Utilisation

Le composant `RadiosField` est utilisable dans un formulaire.

```javascript

import { Form } from "hornet-js-react-components/src/widget/form/form";
import { RadiosField } from "hornet-js-react-components/src/widget/form/radios-field";

/** Référence vers le composant calendrier : nécessaire pour utiliser la fonction setAttribute(). */
private exampleRadio: RadiosField;

constructor(...) {
    /**
    * valeurs présentes dans le radioField
    */
    this.data = [
        {"value": "true", "label": "Client"},
        {"value": "false", "label": "Fournisseur"}
    ];
}

render(): JSX.Element {
    return(
        <div>
            <Form ...>
                <RadiosField
                  name="exampleRadio"
                  label={"Type de partenaire"}
                  dataSource={this.data}
                />
            </Form>
        </div>
    );
}
```

Aperçu :

![radio](../sources/form/radios-field/radio-notinline.png)

Le composant possède les propriétés des interfaces:

[HornetClickableProps](/hornetshowroom/composant/page/hornet-js/composants/proprietes-hornet-component)

[HornetBasicFormFieldProps](/hornetshowroom/composant/page/hornet-js/composants/proprietes-hornet-component)

[HornetComponentDatasourceProps](/hornetshowroom/composant/page/hornet-js/composants/proprietes-hornet-component)

[HornetComponentChoicesProps](/hornetshowroom/composant/page/hornet-js/composants/proprietes-hornet-component)

Attributs du composant RadiosField :

| Attribut     | Description                                                         | Obligatoire |Valeur par défaut | Type     |
| ------------ | ------------------------------------------------------------------- | ----------- |----------------- |--------- |
| id           | Information optionnelle, valorisée par le name si non indiquée      | &nbsp;      | &nbsp;           | string   |
| defaultValue | valeur par défaut à utiliser, doit être à l'image du datasource ou data utilisé.`*`| &nbsp;      | &nbsp;           | any      |
| dataSource   | Données des boutons radio, format: {{"value":"", "label":""}...}    | &nbsp;      | &nbsp;           |Datasource|
| data         | Données des boutons radio en ne passant pas par un Datasource       | &nbsp;      | &nbsp;           | any      |
| label        | Libellé du champ de saisie de date                                  | &nbsp;      | &nbsp;           | string   |
| name         | Nom du radio field                                                  | &nbsp;      | &nbsp;           | string   |
| required     | Indique si la saisie du champ est obligatoire pour valider le formulaire | &nbsp; | false            | boolean  |

`* attention cette valeur sera écrasée par l'utilisation de la méthode updatefields sur le formulaire.`

le champ dispose également d'un attribut inline qui permet de définir comment sont affichés les boutons:

```javascript
...
<RadiosField
  name="exampleRadio"
  label={"Type de partenaire"}
  dataSource={this.data}
  defaultValue={this.data[0]}
  inline={RadiosField.Inline.FIELD}
/>
...
```

![radio](../sources/form/radios-field/radio-inline.png)


```javascript
...
<RadiosField
  name="exampleRadio"
  label={"Type de partenaire"}
  dataSource={this.data}
  defaultValue={this.data[0]}
  inline={RadiosField.Inline.ALL}
/>
...
```

![radio](../sources/form/radios-field/radio-inlineall.png)

la valeur RadiosField.Inline.NONE est la valeur par defaut.


Autre exemple d'utilisation:

```javascript
import { Form } from "hornet-js-react-components/src/widget/form/form";
import { RadiosField } from "hornet-js-react-components/src/widget/form/radios-field";

constructor(...) {
    /**
    * valeurs présentes dans le radioField
    */
    this.data = [
        {"value": "1", "label": "Client"},
        {"value": "2", "label": "Fournisseur"}
        {"value": "3", "label": "autre"}
    ];
}

render(): JSX.Element {
    return(
        <div>
            <Form ...>
                <RadiosField
                 name="exampleRadio"
                 label={"Type de partenaire"}
                 dataSource={this.data}
                 defaultValue={this.data[1]}
                 inline={RadiosField.Inline.FIELD}
                />
            </Form>
        </div>
    );
}

```

![radio](../sources/form/radios-field/radio-exemple.png)

## Live coding

```javascript showroom
	var dataSource = new DataSource<any>([
                {"value": "1", "label": "Client"},
                {"value": "2", "label": "Fournisseur"}
                {"value": "3", "label": "autre"}]);
	return (
            <RadiosField
             name="exampleRadio"
             label={"Type de partenaire"}
             dataSource={dataSource}
             inline={RadiosField.Inline.FIELD}
            />
    );
```