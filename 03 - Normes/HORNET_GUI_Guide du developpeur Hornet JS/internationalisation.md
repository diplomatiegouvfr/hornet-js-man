# Internationalisation

Hornet fourni la possibilité de gérer différentes langues, et la possibilité de modifier les libellés des composants Hornet de base.
Un libellé est défini par une clé utilisée dans les fichiers jsx. Cette clé sera remplacée par un texte.

## Principe

Lorsque le navigateur demande sa première requête HTTP, l'application côtée serveur charge les messages(clés/textes) de l'application.
Ces messages sont utilisés pour construire le flux HTML côté serveur et côté client.

## Fournir les messages à l'application

Hornet supporte deux modes pour passer les messages à l'application: mono et multi language.

## L'application supporte une seule langue

Les messages sont définis dans un fichier JSON. Son contenu est chargé au démarrage du serveur en le passant à l'objet configuration du serveur (fichier server.ts).

les fichiers de langue sont présent dans le répertoire resources (src/resources/messages.json, src/resources/messages-fr-FR.json)

```javascript

import AppI18nLoader = require('src/i18n/app-i18n-loader');

var configServer:serverNs.ServerConfiguration = {
   [........]
   internationalization: new AppI18nLoader(),
   [........]
};
```

Exemple de classe de chargement de l'internationalisation : AppI18nLoader

```javascript
///<reference path='../../node_modules/app/hornet-js-ts-typings/definition.d.ts'/>
"use strict";
import I18nLoader = require('hornet-js-core/src/i18n/i18n-loader');

var messagesFRfr:any = require('src/resources/messages-fr-FR');
var messagesDefault:any = require('src/resources/messages');

/**
 * Classe utilisée uniquement côté serveur.
 */
class AppI18nLoader extends I18nLoader {

    getMessages(locales:Array<string>):any {

        /**
         *  Extraits les messages de fichiers, de base de données....
         *  Doit retourner un flux JSON conform au module react-intl.
         */

        var messages:string;
        var locale:string = '';
        if (locales && locales.length && locales.length > 0) {
            locale = locales[0];
        }
        if (locale === 'fr-FR') {
            messages = messagesFRfr;
        } else {
            messages = messagesDefault;
        }
        return {locale: locale, messages: messages};
    }

}
export = AppI18nLoader;
```

## L'application supporte plusieurs langues

Hornet obtient pour chaque nouvelle requête HTTP, les messages qui correspondent à la langue définis par le navigateur (définis dans le header HTTP).
Pour ce faire, il faut définir une classe au démarrage du serveur qui servira à obtenir les messages suivant la langue. Exemple dans l'application tutoriel:

```javascript
var configServer:serverNs.ServerConfiguration = {  
   [........]
   internationalization: new InternationalizationAppLoader(),
   [........]
};
```

Cette classe respecte le contrat de l'interface `IntlLoader` (fourni par le framework), elle a la responsabilité de fournir les messages en fonction de la langue par la méthode `getMessages()`.

Exemple de cette implémentation dans l'application Tutoriel:

```javascript
class AppInternationalizationLoader extends IntlLoader{
    getMessages(locales:Array<string>):any{
        /**
         *  Extraits les messages de fichiers, de base de données....
         *  Doit retourner un flux JSON conforme au module react-intl.
         */
        var messages:string;
        var locale:string = '';
        if(_.isArray(locales) && locales.length>0 ){
            locale = locales[0];
        }
        if(locale === 'fr-FR'){
            messages = messagesFRfr;
        }else if(locale === 'en-EN'){
            messages = messagesENen;
        }else{
            messages = messagesDefault;
        }
        return {locale: locale,messages:messages};
    }
}
```
Les messages ont pu être chargés dans un fichier ou en base de données

## L'application n'utilise pas l'internationalisation

Dans ce cas, le développeur n'utilise pas de clé dans les jsx mais écrit le texte à afficher.
Il doit passer `null` à l'attribut internationalisation de la configuration serveur. Hornet utilise la langue française et prend des textes définis dans le module `Hornet core`.

```javascript
var configServer:serverNs.ServerConfiguration = {
   [........]
   internationalization: null,
   [........]
};
```

## Redéfinir les messages des composants Hornet

Pour chaque version d'Hornet, les messages des composants sont disponibles dans le module `hornet-js-core` (`require('hornet-js-core/src/internationalization/hornet-messages-components')`).
Lorsque l'application fournit ses messages elle peut donc les redéfinir.

Par exemple, il est possible de modifier les libellés écrits par le composant `hornet-js-form`.

```json
"form": {
    "valid": "Valider",
    "cancel": "Annuler",
  },
```

Changer le libellé du bouton valider:

```json
"form": {
    "valid": "ENREGISTRER",
    "cancel": "NE RIEN FAIRE",
  },
```


## Afficher les messages

Les messages sont accessibles dans les JSX gràce à un mixin:  `var HornetComponentMixin = require('hornet-js-core/src/mixins/react-mixins');`

## Ajouter le Mixin Hornet

Pour utiliser l'internationalisation dans vos composants (`fichier .jsx`), il est nécessaire d'ajouter la Mixin Hornet composant.

Elle permet d'accéder aux messages de votre application et aux méthodes et balises définis par la librairie [Intl-React](http://formatjs.io/react/).

```javascript
var HornetComponentMixin = require('hornet-js-core/src/mixins/react-mixins');
var PartenaireFiche = React.createClass({
    mixins: [HornetComponentMixin],
```

### Messages simples dans une JSX

Les messages sont disponibles sous l'object: `this.context.messages` des composants React (fichier jsx).

Prenons un exemple de message:

```json
{
    "partenairesListePage": {
        "form": {
            "titreFormulaire": "Liste des partenaires",
        }
      }
}
```

Ce message sera accessible dans le fichier jsx avec le nommage suivant:

```
    this.i18n('partenairesListePage.form.titreFormulaire')
```

Ce qui donne dans le fichier `.jsx`:

```javascript
    render: function () {
        var intlMessages = this.i18n('partenairesListePage.form');
        return (
            <div>
                <HornetForm
                    form={this.state.form}
                    titre={intlMessages.titreFormulaire}
                    [...]
```

### Formater un message complexe dans une JSX

#### A l'aide de la  méthode `formatMessage`

Le Mixin `hornetComposantMixin` fourni  des méthodes pour formater les messages avec des variables.

Exemple avec le message suivant (Méthode `formatMessage`):

```json
{
"partenaireFichePage": {
    "titre": "Gestion des partenaires - {nom} {prenom}"
  }
}
```

Les variables 'nom' et 'prenom' sont valorisées comme suit:

```javascript
    render: function () {
        var intlMessages = this.i18n('partenaireFichePage');
        var titre = this.formatMessage(intlMessages.titre, {
            "nom": this.state.form.data.nom,
            "prenom": this.state.form.data.prenom
        });
        [...]
```

#### A l'aide des composants d'internationalisation [Intl-React](http://formatjs.io/react/)

Hornet s'appuie sur la librairie [Intl-React](http://formatjs.io/react/). La librairie permet de formater des nombres, des messages ou des textes.

```javascript
    var FormattedMessage = require('react-intl').FormattedMessage;
    [...]
    render: function () {
        [...]
         <FormattedMessage
                    message={intlMessages.titre}
                    nom={this.state.form.data.nom}
                    prenom={this.state.form.data.prenom} />
        [...]
```

Se reporter à la documentation de [Intl-React](http://formatjs.io/react/).

### Messages dans une action

Les messages sont disponibles dans le context de l'action, par exemple:

```
this.actionContext
    .i18n('contactPage.form.erreurs.manqueTitre');
```
