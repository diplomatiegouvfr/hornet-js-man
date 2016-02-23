# Le composant listes liées

Hornet 5 ne fournit pas de composant dédié pour lier deux listes entre elles, les besoins des applications étant trop variables pour pouvoir extraire un composant utilisable de manière satisfaisante.

Néanmoins l'application tutoriel propose une manière de lier deux listes dans la page d'édition de partenaire.

## Objectif

Lorsqu'une valeur d'une liste est modifiée, une autre liste est modifiée en conséquence.

## Principe implémenté dans l'applitutoriel

- La validation newforms à la saisie (validation: "auto") doit être activée sur le champ pays 
- Avant chaque modification de l'état de validation du formulaire, le choix actuel du pays dans le formulaire est comparé avec celui présent dans le state
- Si le choix est différent :

 - Déclenchement de l'action de chargement des villes présentes dans le pays sélectionné
 - Dès que le store a reçu les données, un évènement "RECEIVE_VILLES_FORM_DATA" est émis
 - La nouvelle liste de villes présentes dans ce pays est alors récupérée dans la page
 - Injection de cette liste dans le composant newforms
 - Calcul d'une valeur par défaut si possible

Voir les fichiers :

- `\applitutoriel-js\src\views\par\par-fpa-form.ts`
- `\applitutoriel-js\src\views\par\par-fpa-page.jsx`
- `\applitutoriel-js\src\stores\par\par-fpa-store.ts`

Extraits du code :

- par-fpa-form.ts :

```javascript
pays: new newforms.ChoiceField({
    custom: {
        useReadOnlyWidget: true
    },
    label: intlMessages.fields.pays.label,
    widget: newforms.Select,
    required: true,
    errorMessages: {
        required: intlMessages.fields.pays.required
    },
    /* Validation automatique uniquement pour ce champ, car il doit déclencher la mise à jour du champ ville */
    validation: "auto",
    choices: paysData
}),
```

- par-fpa-page.jsx :

```javascript
/** Branchement sur les évenements "onChange" déclenchés par les "Stores" */
statics: {
    storeListeners: [FichePartenaireStore]
},

/** Mise à jour spécifique de la page sur un changement de données FichePartenaire  */
onChange: function () {
    logger.debug("FichePartenaires onChange");

    var partenaireStore = this.getStore(FichePartenaireStore);

    /* Met à jour la liste de villes proposées suivant le pays sélectionné */
    var villeSelectionnee = this.state.form.data.ville;
    var champVille = this.state.form.fields.ville;

    var listeVilles = [{id: 0, libelle: this.i18n("partenaireFichePage.form.aucune")}];
    var listeVillesStore = partenaireStore.getListeVille(this.state.form.data.pays);
    if(listeVillesStore) {
        listeVilles = listeVilles.concat(listeVillesStore);
    }
    var choices = newforms.util.makeChoices(listeVilles, "id", "libelle");
    champVille.setChoices(choices);
    var updatedData;
    try {
        champVille.clean(villeSelectionnee);
        updatedData = {ville: villeSelectionnee};
    } catch(validationError) {
        /* La liste de villes a changé et la ville précédemment sélectionnée n'en fait pas partie :
        on sélectionne donc par défaut la première ville de la nouvelle liste */
        updatedData = {ville: choices[0][0]};
    }
    /* On s'assure que l'état de validation du formulaire est à jour pour le champ ville */
    this.state.form.updateData(updatedData);
    /* Force le rendu React, car l'état interne du formulaire a changé, mais pas l'état du composant page */
    this.forceUpdate();
},
```

```javascript
/**
 * Fonction appelée lorsque l'état de validation du formulaire change.
 * Vérifie si le pays sélectionné a changé et déclenche si nécessaire le chargement de la liste de villes correspondante.
 */
onFormChange: function () {
    logger.debug("FichePartenaires onFormChange");
    var nouveauPays = this.state.form.data.pays;
    if (this.state.lastPays != nouveauPays) {
        logger.debug("On Pays Change");
        this.setState({lastPays: nouveauPays});
        logger.debug("Le choix du pays a été modifié, il faut mettre à jour la liste des villes");
        if (nouveauPays && !isNaN(nouveauPays)) {
            this.executeAction(new FichePartenairesAction.ListerVillesParPays().action(), {"id": nouveauPays});
        }
    }
    /* Force le rendu React, car l'état interne de validation du formulaire a changé, mais pas l'état du composant page */
    this.forceUpdate();
},
```

- par-fpa-store.ts :

```javascript
"RECEIVE_VILLES_FORM_DATA": function (villes) {
    logger.debug("RECEIVE_VILLES_FORM_DATA");
    this.villes = villes;
    this.emitChange();
},
```
