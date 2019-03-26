# Le composant CalendarField

Manuel d'utilisation du calendrier Hornet.

## Description

Le composant calendrier, aussi appelé `CalendarField`, associe une zone de saisie de date avec un calendrier et hérite de `InputField`. Le calendrier est accessible en cliquant sur une icône située à droite du champ de saisie.

Champ de saisie avec un calendrier affiché :

![calendrier affiché](../sources/form/calendar-field/calendrier.png)

Le calendrier s'affiche alors en surimpression de la page HTML sans perturber le rendu.

Champ de saisie avec un calendrier caché :

![calendrier caché](../sources/form/calendar-field/date-saisie.png)

Le choix de l'année se fait en cliquant sur l'année indiquée au dessus de la grille du calendrier.

![calendrier affiché](../sources/form/calendar-field/calendrier-annee.png)

![calendrier affiché](../sources/form/calendar-field/calendrier-mois.png)

L'assemblage fait par ce composant a pour but :

- de standardiser les libellés en français par défaut,
- de formater les dates et les zones de saisie selon les conventions françaises par défaut.

La date saisie dans le champ de saisie sera completé lorsque l'utilisateur quitte le champ.
Par exemple, en ne remplissant qu'un numéro de jour, la date sera completée avec le mois et l'année courrante.
En remplissant le jour et le mois, l'année courrante sera ajoutée.

Le calendrier utilisé s'appuie sur le composant `rc-calendar` disponible sous [GitHub](https://github.com/react-component/calendar).

## Utilisation

Le composant calendrier est utilisable dans un formulaire.

```javascript
import { Form } from "hornet-js-react-components/src/widget/form/form";
import { CalendarField } from "hornet-js-react-components/src/widget/form/calendar-field";

/** Référence vers le composant calendrier : nécessaire pour utiliser la fonction setAttribute(). */

render(): JSX.Element {
    return(
        <div>
            <Form ...>
                <CalendarField
                    label="Calendrier"
                    name="exampleCalendar"
                    title="Calendrier"
                />
            </Form>
        </div>
    );
}
```

Le composant possède les propriétés des interfaces:

[HornetWrittableProps](/hornetshowroom/composant/page/hornet-js/composants/proprietes-hornet-component)

[HornetClickableProps](/hornetshowroom/composant/page/hornet-js/composants/proprietes-hornet-component)

[HornetBasicFormFieldProps](/hornetshowroom/composant/page/hornet-js/composants/proprietes-hornet-component)

[ReactFocusDOMAttributes](/hornetshowroom/composant/page/hornet-js/composants/proprietes-hornet-component)

Attributs du composant CalendarField :

| Attribut      | Description                                                          | Obligatoire | Valeur par défaut | Type             |
| ------------- | -------------------------------------------------------------------- | ----------- |------------------ |----------------- |
| dateFormats   | Liste de formats de saisie de date (ex. \["%d/%m/%Y","%d-%m-%Y"\] )  | &nbsp;      | valeur de calendar.dateFormat dans messages.json | array[string] |
| isDatePicker  | Affiche ou non l'icone de sélection d'une date                       | &nbsp;      | true              | boolean          |
| label         | Libellé du champ de saisie de date                                   | &nbsp;      | &nbsp;            | string           |
| placeHolder   | Texte présent dans le champ de saisie lorsque la date n'est pas renseignée | &nbsp;| &nbsp;            | string           |
| title         | Libellé utilisé comme texte alternatif à l'image du bouton d'ouverture de calendrier | &nbsp; | &nbsp; | string           |
| valideOnForm  | Indicateur qui précise que la validation (format) est portée par le formulaire qui contient le composant ou sinon par lui-même | &nbsp; | true | boolean |
| onValueChange | Fonction appelé à chaque modification du champs input (prend la valeur en paramètre) | &nbsp; | &nbsp; | (value: string) => void |
| resettable        | Indique si le champs peut etre réinitialisé ou non                     | &nbsp;      | true               | boolean          |


## Calendar field avec complétion

Une variante du `CalendarField`, le `CalendarAutocomplete` propose un calendar qui vient formater la date saisie par l'utilisateur en y ajoutant les séparateurs.
Ce calendar ne prendra en compte que le premier format de `dateFormats` si celui-ci est renseigné. Les formats pris en compte sont ceux dont les séparateurs sont: "/" , "-" ou "." .

Il s'utilise de la même façon que le CalendarField

```javascript
import { CalendarAutocomplete } from "hornet-js-react-components/src/widget/form/calendar-autocomplete";

    return(
        <CalendarAutocomplete
		  label="Calendrier"
		  name="exampleCalendar"
		  title="Calendrier"/>
    );
```

## Live coding

```javascript showroom
	/** Référence vers le composant calendrier : nécessaire pour utiliser la fonction setAttribute(). */

    return(
        <CalendarField
		  label="Calendrier"
		  name="exampleCalendar"
		  title="Calendrier"/>
    );
```