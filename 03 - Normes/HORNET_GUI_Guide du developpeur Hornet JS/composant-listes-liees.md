# Le composant liste liées

Hornet 5 ne fournit pas de composant dédié pour lier deux listes entre elles.
En effet les besoins des applications sont trop variables pour pouvoir extraire un composant utilisable de manière satisfaisante.

Néanmoins l'application tutoriel propose une manière de lier deux listes dans la page d'édition de partenaires.

## Objectif

Lorsqu'une valeur d'une liste est modifiée, une autre liste est modifiée en conséquence.

## Principe implémenté dans l'appli-tuto

Voir le fichier: `\applitutoriel\applitutoriel-js\src\views\par\par-fpa-page.jsx`

Extrait du code:

```javascript
componentWillUpdate: function (nextProps, nextState) {
        var partenaireStore = this.getStore(PartenaireStore);
        var nouveauPays = this.state.form.data.pays;
        if (this.state.lastPays != nouveauPays) {
            // Le choix du pays a été modifié, il faut mettre à jour la liste des villes
            var choix = newForms.util.makeChoices(partenaireStore.getListeVille(nouveauPays), 'id', 'libelle');
            if (choix && choix.length > 0) {
                // Indication d'une valeur par défaut
                this.state.form.data['ville'] = choix[0][0];
            }
            this.state.form.fields.ville.setChoices(choix);
            // Mise à jour de l'IHM
            this.setState({lastPays: nouveauPays});
        }
    },
```

 - Avant chaque rendu de la page, le choix actuel du pays dans le formulaire est comparé avec celui présent dans le state
 - Si le choix est différent:
  - Demande est faite au store de fournir une nouvelle liste de villes présentes dans ce pays
  - Injection de cette liste dans le composant newforms
  - Calcul d'une valeur par défaut si possible
  - Mise à jour du state avec le pays actuellement sélectionné
