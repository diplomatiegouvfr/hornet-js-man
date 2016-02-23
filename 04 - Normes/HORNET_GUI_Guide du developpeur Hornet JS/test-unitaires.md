# Tests unitaires

## Outils

* `[chai](http://chaijs.com/)` : Bibliothèque d'assertions pour Node
* `[chai-jquery](https://github.com/chaijs/chai-jquery)` : Assertions complémentaires sur les objets jQuery pour chai
* `[cheerio](https://github.com/cheeriojs/cheerio)` : Implémentation de jQuery côté serveur permettant de faciliter la manipulation du code HTML dans les tests
* `[mocha](http://mochajs.org/)` : Framework de tests JavaScript
* `[sinonJS](http://sinonjs.org/)` : Framework de bouchonnages de fonctions
* `[proxyquire](https://github.com/thlorenz/proxyquire)` : Framework de bouchonnage des `require` côté NodeJS

## How-To

### Composant JavaScript Simple

Le composant à tester `src/js/Hello.js` suivant :

``` javascript
module.exports = function(name) {
    return "hello " + name;
};
```

Le code de test correspondant `test/HelloSpec.js` peut être le suivant :

```javascript
var chai = require("chai");
var expect = chai.expect;
var hello = require("src/Hello");

describe("Test JS", function() {
    it("doit passer", function() {
        expect(hello("world")).to.be.equal("hello world");
    });
});
```

Ce test fait uniquement appel à l'outil `chai`.

### Composant React

Dans le cadre des tests d'un composant React (syntaxe JSX), les outils `chai-jquery`, `cheerio` et `mocha` sont également utilisés.

Par exemple, dans le cadre du test du composant React simple suivant :

```javascript
var React = require("react");
var FormButton = React.createClass({
    render: function () {
        return (
            <button type={this.props.item.type}
					id={this.props.item.id}
					name={this.props.item.name}
					value={this.props.item.value}
					className={this.props.item.className}>
						{this.props.item.label}
			</button>
        );
    }
});
module.exports = FormButton;
```

Le fichier de test associé pourrait être :

```javascript
var TestUtils = require("hornet-js-utils/src/test-utils");
var React = require("react");
var expect = TestUtils.chai.expect;
var render = TestUtils.render;
var Button = require("hornet-js-components/button/button");
describe("Button", function () {
    it("doit être configuré avec les informations paramétrées", function () {
        // Arrange
        var item = {
            type: "submit",
            id: "form_btnTest",
            name: "btnTest",
            value: "test value",
            className: "hornet-button",
            label: "test label"
        };
        // Act
        var $ = render(() => <Button key={item.id + "_" + item.name} item={item} />),
            $button = $("button");
        expect($button).to.have.attr("type", item.type);
        expect($button).to.have.attr("id", item.id);
        expect($button).to.have.attr("name", item.name);
        expect($button).to.have.attr("value", item.value);
        expect($button).to.have.class(item.className);
        expect($button).to.have.text(item.label);
    });
});
```

### Tester une fonction d'un composant React

Il est possible de tester une fonction donnée d'un composant React en instanciant la classe associée.

Dans ce cas il est préférable d'utiliser le librairie `sinonJs` afin de bouchonner les méthodes et tester leur appel.

Un fichier de test peut-être:

```javascript
var React = require("react/addons");
var TestUtils = require("hornet-js-utils/src/test-utils");
var expect = TestUtils.chai.expect;
var render = TestUtils.render;
var sinon = TestUtils.sinon;
var proxyquire = require("proxyquire").noCallThru();
var fluxInformationsStoreMock = sinon.stub();
var modalComponentMock = require("test/dialog/generic-react-mock")();
var Spinner = proxyquire("hornet-js-components/src/spinner/spinner", {
    "hornet-js-core/src/stores/flux-informations-store": fluxInformationsStoreMock,
    "src/dialog/modal": modalComponentMock
});
describe("SpinnerReactComponent", function () {
    beforeEach(function () {
        this.context = {
            getStore: sinon.stub().returns(fluxInformationsStoreMock)
        };
        fluxInformationsStoreMock.hasActionsRunning = sinon.stub();
    });
    it("doit récupérer la valeur true du store et propager la visibilité", function () {
        // Arrange
        var instance = new Spinner();
        instance.getStore = this.context.getStore;
        fluxInformationsStoreMock.hasActionsRunning.returns(true);
        instance.setState = sinon.spy();
        // Act
        instance._retrieveStoreValue();
        // Assert
        sinon.assert.calledWith(instance.setState, sinon.match.has("isVisible", true).and(sinon.match.has("lastVisibilitySwitch")));
        expect(fluxInformationsStoreMock.hasActionsRunning).to.be.calledOnce;
    });
});
```

# Couverture de test

## Outils

La couverture de code se base sur les tests unitaires effectués sur les composants JavaScript, TypeScript et JSX (React).

* [istanbul](https://www.npmjs.com/package/istanbul) : Framework de calcul de couverture de code

## How-To

* Se positionner dans le module souhaité, ou dans le module parent pour obtenir la couverture complète.
* Lancer la commande `hb test`

Cette commande peut également être jouée pour tous les modules du framework et produira un rapport pour chaque module.

Cette commande lance les tests développés précédemment et produit un rapport de couverture comme suit :

```shell
--------------------------------------|----------|----------|----------|----------|----------------|
File                                  |  % Stmts | % Branch |  % Funcs |  % Lines |Uncovered Lines |
--------------------------------------|----------|----------|----------|----------|----------------|
 src\                                 |    78.13 |    46.67 |      100 |    82.14 |                |
  hornet-component.js                 |    78.13 |    46.67 |      100 |    82.14 | 31,32,33,36,38 |
 src\auto-complete\                   |    53.88 |     20.2 |    48.72 |    53.66 |                |
  auto-complete-field.js              |      100 |      100 |      100 |      100 |                |
  auto-complete-selector.js           |    91.67 |      100 |    71.43 |    91.43 |       26,43,44 |
  auto-complete-widget.js             |    81.48 |       50 |    66.67 |    81.48 | 17,48,50,76,78 |
  auto-complete.js                    |    33.59 |    16.13 |       36 |    33.59 |... 384,391,392 |
 src\button\                          |    90.91 |    63.64 |    85.71 |    92.59 |                |
  button-props.js                     |      100 |      100 |      100 |      100 |                |
  button.js                           |    90.91 |    63.64 |    85.71 |    92.59 |          25,26 |
 src\calendar\                        |    64.46 |    55.81 |    42.11 |    64.46 |                |
  calendar.js                         |    43.82 |    33.33 |    23.08 |    43.82 |... 240,244,245 |
  date-picker-field.js                |    91.67 |    81.82 |      100 |    91.67 |    34,40,45,78 |
  date-picker-input.js                |    82.76 |    63.64 |    66.67 |    82.76 | 39,40,41,42,45 |
 src\chart\                           |    26.97 |        0 |     6.52 |    27.15 |                |
  chart-donut.js                      |    26.97 |        0 |     6.52 |    27.15 |... 298,305,313 |
 src\dialog\                          |    55.24 |    29.44 |     37.5 |    53.96 |                |
  alert-props.js                      |      100 |      100 |      100 |      100 |                |
  alert.js                            |    78.26 |    40.54 |    33.33 |     77.5 |... 70,78,86,94 |
  modal-props.js                      |      100 |      100 |      100 |      100 |                |
  modal.js                            |    98.15 |    62.22 |    90.91 |      100 |                |
  react-aria-modal.js                 |     37.1 |    13.04 |    21.21 |    36.16 |... 247,248,253 |
 src\form\                            |    52.13 |    33.96 |    57.14 |    52.13 |                |
  form.js                             |    52.17 |    35.48 |    47.06 |    52.17 |... 184,292,293 |
  grid-form.js                        |    54.03 |    35.83 |    72.73 |    54.03 |... 397,415,448 |
  read-only-widget.js                 |    38.89 |        0 |        0 |    38.89 |... 29,30,31,34 |
 src\icon\                            |    81.58 |       60 |    85.71 |    81.25 |                |
  icon-props.js                       |      100 |      100 |      100 |      100 |                |
  icon.js                             |    81.58 |       60 |    85.71 |    81.25 |... 31,34,37,38 |
 src\navigation\                      |    65.99 |    53.68 |    65.31 |    65.99 |                |
  bread-crumb-item.js                 |      100 |    93.75 |      100 |      100 |                |
  bread-crumb.js                      |    91.49 |      100 |    66.67 |    91.49 |    57,61,65,66 |
  menu-constantes.js                  |      100 |      100 |      100 |      100 |                |
  menu-infos-complementaires.js       |      100 |      100 |      100 |      100 |                |
  menu-link.js                        |       80 |    68.75 |    44.44 |       80 |... 66,67,69,91 |
  menu-navigation.js                  |    30.56 |    18.07 |       40 |    30.56 |... 399,400,401 |
  menu.js                             |     97.5 |    80.77 |      100 |     97.5 |            107 |
  plan.js                             |    94.29 |    78.95 |      100 |    94.29 |          75,81 |
 src\navigation\store\                |    71.43 |    71.43 |     37.5 |       68 |                |
  navigation-base-store.js            |    71.43 |    71.43 |     37.5 |       68 |... 23,30,31,32 |
 src\navigation\utils\                |    76.32 |       25 |    57.14 |    76.32 |                |
  navigation-utils.js                 |    76.32 |       25 |    57.14 |    76.32 |... 67,70,78,80 |
 src\notification\                    |    78.95 |    60.92 |    68.97 |    78.43 |                |
  notification-message-item.js        |    77.27 |    56.41 |    77.78 |    76.32 |... 30,31,34,42 |
  notification-props.js               |      100 |      100 |      100 |      100 |                |
  notification.js                     |       80 |    64.58 |       65 |    79.69 |... 86,87,89,93 |
 src\spinner\                         |    93.65 |    71.11 |    76.92 |    94.74 |                |
  spinner-props.js                    |      100 |      100 |      100 |      100 |                |
  spinner.js                          |    93.65 |    71.11 |    76.92 |    94.74 |       76,84,92 |
 src\tab\                             |    94.74 |    64.06 |       85 |    97.56 |                |
  tab-props.js                        |      100 |      100 |      100 |      100 |                |
  tab.js                              |    97.22 |    57.58 |      100 |      100 |                |
  tabs.js                             |    93.22 |    70.97 |    76.92 |    96.15 |          28,66 |
 src\table\                           |     62.9 |    47.17 |    56.93 |     62.9 |                |
  table-caption.js                    |       75 |       50 |       50 |       75 |    17,18,35,36 |
  table-content.js                    |    85.71 |      100 |      100 |    85.71 |          59,60 |
  table-filters.js                    |    44.12 |    21.43 |    22.73 |    44.12 |... 317,321,325 |
  table-header-column.js              |    84.62 |    67.27 |    66.67 |    84.62 |... 140,178,183 |
  table-header-mass.js                |    65.22 |    33.33 |     62.5 |    65.22 |... 125,126,127 |
  table-header.js                     |    88.24 |      100 |      100 |    88.24 |          94,95 |
  table-row.js                        |    67.35 |    52.94 |    63.64 |    67.35 |... 140,181,182 |
  table-rows.js                       |    75.86 |    68.42 |       80 |    75.86 |... 62,63,94,95 |
  table-title.js                      |    71.43 |       20 |    66.67 |    71.43 |    30,31,49,50 |
  table-tools-action-add.js           |    78.26 |     62.5 |    83.33 |    78.26 |62,63,74,75,100 |
  table-tools-action-export-button.js |       32 |        0 |    16.67 |       32 |... 107,112,113 |
  table-tools-action-export.js        |    66.67 |     12.5 |    42.86 |    66.67 |... 84,89,93,97 |
  table-tools-action-filter.js        |     67.5 |    30.43 |    55.56 |     67.5 |... 127,131,135 |
  table-tools-action-mass.js          |    78.95 |       75 |    83.33 |    78.95 |    58,59,71,72 |
  table-tools-action-pagination.js    |     62.5 |    55.32 |    69.23 |     62.5 |... 269,271,273 |
  table-tools-bottom.js               |    86.67 |      100 |      100 |    86.67 |          72,73 |
  table-tools-top.js                  |     87.5 |      100 |      100 |     87.5 |          76,77 |
  table.js                            |    46.92 |       50 |    57.69 |    46.92 |... 571,573,575 |
 src\table\actions\                   |    49.18 |    23.81 |       50 |    46.55 |                |
  table-actions.js                    |    49.18 |    23.81 |       50 |    46.55 |... 79,83,85,88 |
 src\table\store\                     |    85.12 |    75.68 |    81.82 |    84.75 |                |
  table-store-data.js                 |    72.73 |      100 |    66.67 |    72.73 |... 15,22,23,24 |
  table-store-interface.js            |      100 |      100 |      100 |      100 |                |
  table-store.js                      |    87.88 |    75.68 |     87.5 |     87.5 |... 110,113,114 |
 src\tool-tip\                        |    96.88 |    51.61 |      100 |      100 |                |
  tool-tip-props.js                   |      100 |      100 |      100 |      100 |                |
  tool-tip.js                         |    96.88 |    51.61 |      100 |      100 |                |
 src\upload-file\                     |     51.9 |    18.37 |       50 |     51.9 |                |
  upload-file-field.js                |    48.28 |     9.52 |    33.33 |    48.28 |... 48,50,51,56 |
  upload-file-widget.js               |       54 |       25 |    57.14 |       54 |... 108,117,120 |
--------------------------------------|----------|----------|----------|----------|----------------|
All files                             |    63.36 |    44.14 |    54.49 |    62.87 |                |
--------------------------------------|----------|----------|----------|----------|----------------|
```

Un rapport au format HTML est accessible sous target/istanbul/reports/**.
