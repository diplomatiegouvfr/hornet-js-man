# Guide d'ecriture Hornet

## Convention de nommage

- Les fichiers sont nommés avec des minuscules, avec un `-` comme séparateur de mots.

### Composants React

- Les composants React faisant partie du framework sont prefixés par Hornet, puis le nom du composant en anglais. exemple: `HornetButton`.
- Les composants React écrits dans l'application ont un nom provenant de leur specification fonctionnelle.

Les composants React qui correspondent à des écrans doivent finir par `page`.
Exemple: `gen-cnt-page.jsx`

### Variables

- anglais
- minuscule

### Constantes

- anglais
- ALL_UPPER_CASE

### Methodes

- anglais
- verbe en minuscule, puis camelCase. exemple `moveAccount()`

### Paramètres

- anglais
- minuscule

## Les Commentaires

Les commentaires sont écrits en français.

### Commentaires d'entête

Cartouche (voir licence).

### Ligne de commentaires

Pour une meilleure lisibilité, privilégier les commentaires documentaires en entête de fonction et les logs en debug.

### Commentaires documentaires

Identique à javadoc. En français.

### Formatage

Utilisation de Webstorm, `Ctrl+Alt+L` ou `Code -> ReformatCode`

## Bonnes Pratiques

### le mode `strict`

Le mode `strict` est vivement conseillé afin de renforcer la qualité du code typescript.

[Strict Mode](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode)

#### mise en oeuvre

Ajouter au debut de chaque fichier .ts la ligne suivante : "use strict";

#### import d'une classe d'un autre module

exemple :
```javascript
// Dans une fichier JSX >> var
var HornetComponentMixin = require('hornet-js-core/src/mixins/react-mixins');

// Dans un fichier TS ou TSX >> import
import HornetComponentMixin = require('hornet-js-core/src/mixins/react-mixins');
```
 
Pour éviter les confusions, le nom de la variable doit respecter la règle suivante :
- si composant React (jsx ou tsx) : Nom de la classe/variable d'origine préfixé par "Hornet".
- si classe javascript (ts) : Nom de la classe d'origine
