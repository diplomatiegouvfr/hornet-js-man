# Bonnes pratiques CSS et Préprocesseurs (SCSS / SASS)


## Rappels & bonnes pratiques CSS :
Voici quelques règles appliquées dans les feuilles de style de l'application

**Valeurs à 0 :**
Lorsqu'une valeur est à 0, il est recommandé de ne pas spécifier d'unité de mesure ensuite (px, em, % etc...). Les valeurs avec décimal telles que `0.5em` doivent s'écrire telle quel et non `.5em`.

**Codes couleurs :**
Pour les codes hexadecimaux des couleurs, conserver les minuscules pour une meilleure lisibilité `#FED8DA` devient `#fed8da`

**Opacité des couleurs :**
Les règles rgba() des couleurs permet d'appliquer une notion d'opacité aux couleurs (a qui prend une valeur entre 0 et 1) pratique pour décliner une même couleur ex: `#fed8da` peut se traduire par `rgba(254, 216, 218, 1)`

**Définition des classes :**
Il n'est pas nécessaire de sur-spécifier les chemins pour atteindre une classe. Cela résultera à une perte de performance lors du rendu. Par exemple :
`#header ul li a.header-link` peut se résumer en `.header-link`

**Utiliser les syntaxes raccourcies :**
La plupart des attributs CSS peuvent être raccourcis pour une meilleure lisibilité et moins de code et donc de meilleures performances d'affichage. Voici des exemples des plus fréquentes :
```sass
.maClasse {
	padding-top: 1em;
	padding-bottom: 1em;
	padding-left: 0.5em;
	padding-right: 0.5em;
	background-image: url(monImage.svg);
	background-repeat: no-repeat;
	background-position: top center;
	background-color: red;
	transition-property: color;
	transition-timing-function: ease-in-out;
	transition-duration: 0.3s;
}
// Peut se résumer en :
.maClasse {	
	// padding: top right bottom left; 
	padding: 1em 0.5em 1em 0.5em;
	// ou encore padding: top/bottom right/left quand les valeurs sont jumelles
	padding: 1em 0.5em;
	// background: image / repeat / position / color
	background: url(monImage.svg) no-repeat top center red;
	// transition: property / duration / timing function
	transition: color 0.3s ease-in-out;	
}
```
`margin`, `padding`, `background`, `border`, `animation`, `flex`, `grid`, `transition` entre autre, possèdent des valeurs qui peuvent être raccourcies.

**Les sélecteurs :**
Permettent de cibler précisément des éléments enfants d'un parent sans pour autant ajouter des classes sur chaque élément.
ul li :  l'espace définit tous les éléments li d'un ul
ul **>** li : `>` définit les éléments uniquements enfants d'un ul

*Exemple :*
```html
<ul>
  <li></li>
  <li></li>
  <li>
    <ol>
      <li></li>
      <li></li>
    </ol>
  </li>
  <li></li>
</ul>
```
```sass
//tous les li auront un background rouge
ul li {
  background: red;
}
// seuls les li du ul auront un background bleu (pas les li du ol)
ul > li {
  background: blue;
}
```
h1+p:  `+` Cible l'enfant directement adjacent à son parent (ici le premier p suivant un h1)
h1~p: `~` Cible tous les enfants directement adjacents au h1

*Exemple :*
```html
<h1>Titre</h1>
<p>Lorem</p>
<p>Ipsum</p>
<p>Dolor</p>
```
```sass
// Seul le premier p aura le fond violet
h1+p {
  background: purple;
}
// Tous les p adjacents au h1 auront le fond rose
h1~p {
  background: pink
}
```
**[title~=state] :**  `~` Cible **tous les élements** dont l'attribut title **contient** le mot "state"

**[lang|=fr] :** `|` Cible **tous les éléments** avec un attribut lang **commençant** par "fr"

**a[href^="https"] :** `^` Cible **tous** les liens dont l'attribut href **commence** par "https"

**a[href$=".pdf"] :** `$` Cible **tous** les liens dont l'attribut href **termine** par "https"

**a[href*="w3schools"] :** `*` Cible **tous** les liens dont l'attribut href **contiens** la chaine "art" (attention la déclaration est **sensible à la casse**).


**Pseudo-classes :**
Outre les pseudos classes les plus connues comme `:active`, `:hover`, `:before`, `:after` etc... en voici d'autres, pratiques pour cibler des éléments html précis sans nécessiter de classes.

**:not(n) : ** (Pseudo-classe de négation) Sélectionne un élément qui n'est pas de type n `div:not(.two)` ciblera par exemple toutes les divs qui n'ont pas la classe `.two`

**:nth-child(n) :** (ou Les pseudo-classes structurelles) Cible un élément qui est le **n-ième enfant** de son parent. `tr:nth-child(4)` ciblera donc la 4ème ligne d'une tableau. `tr:nth-child(odd)` et `tr:nth-child(even)` cibleront respectivement les enfants pairs et impairs d'un parent.

**:nth-last-child(n) :** Cible un élément qui est le **n-ème enfant** de son parent **mais en partant à partir du dernier enfant**

**:nth-of-type(n) :** cible un élément qui est le **n-ième frère** du même type.

**:nth-last-of-type(n) :** cible un élément qui est le **n-ième frère** du même type **en partant du dernier frère**

**:first-child :** cible un élément qui est le **premier enfant** de son parent.

**:last-child :** cible un élément qui est le **dernier enfant** de son parent.

**:first-of-type :** cible un élément qui est le **premier frère** de son type.

**:last-of-type :** cible un élément qui est le **dernier frère** de son type.

**:only-child :** cible un élément qui est **le seul enfant** de son parent.

**:only-of-type :** cible un élément qui est le **seul de son type dans sa fratrie**.

**:empty :** cible un élément qui **n'a pas d'enfants, ni de texte**. C'est une façon de cibler les attributs vides

## Syntaxes indispensables SASS/SCSS :
**Nesting :**
La force des pré-processeur est de pouvoir enchainer les styles en répétant le moins de code possible. Le nesting permet de créer un héritage de classe de façon évidente par la syntaxe.

*En prenant l'exemple d'un input  et de liens contenus dans une div de classe .autocomplete*
*En css :*
```css
.autocomplete {
	//style de la classe
}
.autocomplete input {
	//style de l'input
}
.autocomplete a {
	//style du lien
}
.autocomplete a:hover {
	//style du comportement hover du lien
}
```
*En scss :*
```sass
.autocomplete {
	//style de la classe
	input {
		//style de l'input
	}
	a {
		//style du lien
		&:hover {
			//style du comportement du hover
		}
	}
}
```
**Opérateurs :**
Le `&` permet de préciser un héritage lors d'un nesting. Il permet de gagner du temps et des lignes de code en plus d'affiner la logique du css.
Exemple :
```sass
// scss
.maClasse {
	color: red;
	font-size: 1em;
	margin: 2em;

	&.monAutreClasse {
		padding: 1em 0;
	}
}
a {
	color: purple;
	transition: color 0.3s ease-in-out;
	&:hover {
		color: orange;
	}
}
// css
.maClasse {
	color: red;
	font-size: 1em;
	margin: 2em;
}
.maClasse.monAutreClasse {
	padding: 1em 0;
}
a {
	color: purple;
	transition: color 0.3s ease-in-out;
}
a:hover {
	color: orange;
}
```
Dans ces exemples le `&` permet de conserver l'héritage tout en rendant le code plus clair lorsqu'il est positionné en début de ligne. Il peut aussi être mis en fin de ligne pour qualifier un contexte différent à l'élément ciblé.

*Exemple :*
```sass
// scss
.button {
  body.main & { }
}
// css
body.main .button {}
```
Ici la classe `.bouton` peut hériter d'un style particulier dès lors qu'il se trouve dans un `body` de classe `.main`

**variables :**
Se déclarent ici avec un `$` en SCSS ou SASS (avec un `@` en LESS)

**Concaténation :**
Pour concaténer une variable à une chaîne il faut la contenir entre `#{}`. Un exemple en ajouter une variable de media querie dans une feuille de style `@media (max-width: #{$mediaXS-405}) { ... }`


**mixins :**
Les mixins sont des "fonctions" qui sont déclarées via `@mixin nomDuMixin($arguments)` et s'utilise via `@include nomDuMixin($arguments)`. Elles ajoutent un dynamisme à la création des styles, permettent d'optimiser le code et les performances.

*Exemple de mixin puis de leur utilisation dans les feuilles de style :*

```sass
// Mixin permettant de générer un background en svg
// les arguments facultatifs sont déclarés en fin de déclaration et possèdent forcément une valeur par défaut
@mixin svgAsBackground($color, $url, $position : center) {
    -webkit-mask: url($url) no-repeat $position;
    mask: url($url) no-repeat $position;
    background-color: $color;
}
// Mixin permettant d'industrialiser la création d'un menu
// les @if fonctionnent comme n'importe quel autre langage
@mixin createMenu($tranform, $color, $position: "") {
    position: relative;
    display: inline-block;
    width: 0.4em;
    height: 0.4em;
    left: 1em;

    @if $position != "" {
        bottom: $position
    }

    content: "";
    border-right: 0.15em solid $color;
    border-top: 0.15em solid $color;
    -webkit-transform: rotate($tranform);
    -moz-transform: rotate($tranform);
    -o-transform: rotate($tranform);
    transform: rotate($tranform);
}
```
*Utilisation :*
```sass
.more-actions {
    display: inline-block;
    height: 1.5em;
    width: 1.5em;
    vertical-align: middle;
	// inclusion du mixin et de ses arguments valorisés
    @include svgAsBackground($ddBaseColor, "more.svg", 'left top');
}
.having-sub-nav {
	span {
		&:after {
			@include createMenu(-45deg, $color: $baseColorDark);
		}
	}
}
```
**Fonctions :**
les fonctions sont différentes des mixins en ce sens qu'**elles ne portent que des valeurs d'attributs de style**. Elles permettent de calculer par exemple des valeurs complexes et autres mesures dynamiques de certains éléments.
Elles sont déclarées via `@function nomFonction($args...){}` et retournent les valeurs via `@return`. Elles s'activent en appelant simplement la fonction **dans l'attribut d'un style**.

*Exemple :*
```sass
//déclaration
@function maFonction($arg1, $arg2) {
	@return $arg1 + $arg2
}
//utilisation
.element {
	padding: maFonction(20px, 15px);
}
```

**Placeholders :**
Les placeholders ressemblent à des classes si ce n'est qu'elles **ne sont pas compilées tant qu'elles ne sont pas appelées**. Un placeholder débute par un `%nomDuPlacehorder` suivi du style voulu. Déclarées dans la feuille de style, elles sont invisibles tant que `@extend %nomDuPlacehorder` n'est pas spécifié dans le code.
Elles permettent de factoriser du code qui se répète sans pour autant avoir besoin d'un mixin ou d'une fonction. Les placeholders sont **recommandés pour les styles statiques**, c'est-à-dire qu'elles ne peuvent changer en fonction de variables relatives au contexte

*Exemple :*

```sass
//déclarée ainsi, ce code ne sera pas compilé même s'il est présent dans la feuille de style
%center {
	display: block;
	margin-left: auto;
	margin-right: auto;
}

//Le placeholder devient compilé une fois déclaré dans le code via @extend
container {
	@extend %center;
}
```

**Les listes imbriquées (nested lists) & maps :**
Les listes imbriquées permettent de stocker des informations dans une liste qui sera ensuite parsée via des commandes telle que `@each`, à la manière de boucles classiques. Cependant, les parcourir requiert des mécanismes peu pratiques et clairs.

*Voici un exemple :*

```sass
// Voici la liste imbriquée :
$message-types: (
    (error #b94a48)
    (valid #468847)
    (warning #c09853)
    (info #3a87ad)
);

// et comment la parcourir :
@each $message in $message-types {
    //voici comment récupérer la première valeur
    $type : nth($message, 1);
    //voici comment récupérer la seconde valeur
    $color : nth($message, 2);

    //Ici l'action à réaliser pour générer du style css dynamique
    ...
}

```
Outre l'appel à `@each`, on remarque `nth()` qui permet de cibler une valeur précise de la liste imbriquée.


Mais avec SASS 3.3, les maps sont apparues. A la façon d'objets ou d'array, elles permettent de stocker des clés/valeurs et les parcourir est plus simple.

*Exemple :*

```sass
// Voici la map (attention à la syntaxe qui change) :
$message-types: (
    error: #b94a48,
    valid: #468847,
    warning: #c09853,
    info: #3a87ad
);

// et comment la parcourir :
@each $type, $color in $message-types {
    //Ici l'action à réaliser pour générer du style css dynamique
    ...
}

```

Parcourir les clés/valeurs est bien plus simple. les maps sont embarquées avec des fonctions natives telles que :

**map-get($map, $key):** retourne la valeur de la $map ayant pour clé $key (renvoi null en cas d'abscence de valeurs).

**map-merge($map1, $map2):** fusionne deux maps

**map-remove($map, $key):** retourne une nouvelle map en supprimant la clé spécifiée

**map-keys($map):** retourne la liste des clés de la $map

**map-values($map):** retourne la liste des valeurs

**map-has-key($map, $key):** test l'existence de la clé dans la map spécifiée