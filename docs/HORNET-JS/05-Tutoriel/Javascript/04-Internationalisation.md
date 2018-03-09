# Internationalisation

Hornet fournit la possibilité de gérer différentes langues, et la possibilité de modifier les libellés des composants Hornet de base.

Un libellé est défini par une clé utilisée dans les fichiers tsx. Cette clé sera remplacée par un texte.

## Principe

Lorsque le navigateur demande sa première requête HTTP, l'application côté serveur charge les messages(clés/textes) de l'application.

Ces messages sont utilisés pour construire le flux HTML côté serveur et côté client.

## Fournir les messages à l'application

Hornet supporte deux modes pour passer les messages à l'application: mono et multi language.
Les messages sont définis dans un fichier JSON. Son contenu est chargé au démarrage du serveur en le passant à l'objet configuration du serveur (fichier server.ts).

## Fonctionnement 

La gestion des langues se trouve dans hornet-js-core avec la classe **i18n-loader**. Cette classe fonctionne en plusieurs étapes en fonction des fichiers json pour les langues disponibles.

Ce module utilise 2 méthodes :
- getMessages() : retourne le json de langue selectionné en fonction des disponibilités
- getLocales() : liste les langues disponibles dans le dossier resources si il y en a.

```javascript
export class I18nLoader {

    constructor(public pathLang?: string) {
        if (!pathLang) {
            pathLang = path.join(path.dirname(require.main), "src", "resources");
        }
    }
    /** Méthode qui retourne la langue selectionné */ 
    getMessages(locales?: II18n):any {
        { ........ }
        return {locale: locales.locale, lang: locales.lang, messages: messages};
    }
    /** Méthode qui liste les langues disponibles dans le dossier resources */
    getLocales(): Array<string> {
        { ........ }
        return testarray;
    }
}
```

La module i18n est fourni avec un fichier par défaut (hornet-messages-component.json) qui permet aux différents composants de fonctionner.
La méthode **getMessages()** retourne la langue en fonction des disponibilités des fichiers json de l'application dans l'ordre ci dessous :

1. messages-fr-FR.json
2. messages.json 
3. hornet-messages-component.json 

*Le chiffre le plus petit est prioritaire*
 
### Une seule langue

Pour un fonctionnement mono-langue, il faut avoir un fichier json message.json dans le répertoire resources (src/resources/messages.json).


### Plusieurs langues

Pour configurer la langue par défaut de l'application, il suffit de remplir simplement dans le fichier de config **default.json** . 
Si jamais aucune langue n'a été renseignée, c'est le fichier `~/src/resources/messages.json` qui sera chargé par défaut. 
Les fichiers de langue sont présents dans le répertoire resources (src/resources/messages.json, src/resources/messages-fr-FR.json)

```json
"localeI18n": {
    "locale": "en-EN",
    "lang": "en"
  },
```

Pour charger le fichier de langue, il faut définir une classe au démarrage du serveur qui servira à obtenir les messages suivant la langue. Exemple dans l'applitutoriel :

```javascript
var configServer:ServerConfiguration = {  
   ...
   internationalization: new AppliI18nLoader(),
   ...
};
```
Créer une classe qui étendra le module i18n du core et qui prendra en paramètre le chemin du dossier contenant les sources . 
Ex : `/src/resources/`

```javascript
export class AppliI18nLoader extends I18nLoader {

    constructor() {
        super(path.join(__dirname, "..", "resources"));
    }
}
```


### Pas d'internationalisation

Il doit passer `null` à l'attribut internationalisation de la configuration serveur : `server.ts`. Hornet utilise la langue française : `fr-FR` et prend des textes définis dans le module `hornet-js-core/src/i18n/hornet-messages-components.json`.

```javascript
var configServer:ServerConfiguration = {
   ...
   internationalization: null,
   ...
};
```

## Redéfinir les messages des composants Hornet

Pour chaque version d'Hornet, les messages des composants sont disponibles dans le module `hornet-js-core` (`require("hornet-js-core/src/internationalization/hornet-messages-components")`).

Lorsque l'application fournit ses messages elle peut donc les redéfinir.

Par exemple, il est possible de modifier les libellés écrits par le composant `Form`.

```json
form: {
    valid: "Valider",
    cancel: "Annuler",
  },
```

Changer le libellé du bouton valider:

```json
form: {
    valid: "ENREGISTRER",
    cancel: "NE RIEN FAIRE",
  },
```

## Afficher les messages

Les messages sont accessibles dans les au sein d'un composant React (*.tsx) dès lors que ce dernier hérite de `HornetComponent` grâce à la méthode `this.i18n('myKey');` 

### Format des clés

Les clés utilisées pour stocker les messages internationalisés doivent être composées de chaînes de caractères alphanumériques non accentués, ou de tirets (caractères `-` ou `_`). 

Les points servent à séparer les différents niveaux de blocs de messages lors de leur recherche grâce à la fonction i18n.

Exemple :

messages.json :

```json
{
    partenairesListePage: {
        form: {
            titreFormulaire: "Liste des partenaires",
      		sousTitreFormulaire: "Recherche"
        },
    	tableau: {
      		iconConsulter: "Consulter la fiche de {nom} {prenom}",
      		iconEditer: "Editer la fiche de {nom} {prenom}"
      	}
      }
}
```

Récupération d'un ensemble de messages :

```javascript
let pageMessages = this.i18n("partenairesListePage");
let formMessages = this.i18n("partenairesListePage.form");
```

Récupération directe d'un message :

```javascript
let titre = this.i18n("partenairesListePage.form.titreFormulaire");
```

### Message par défaut

Pour une clé recherchée, lorsqu'aucun message n'est trouvé dans les messages de l'application ou des composants Hornet, la clé est utilisée telle quelle. Un message d'avertissement est alors ajouté au log.

### Messages simples dans une tsx

Les messages sont disponibles sous l'object: `this.context.messages` des composants React (fichier tsx).

Prenons un exemple de message:

```json
{
    partenairesListePage: {
        form: {
            titreFormulaire: "Liste des partenaires",
        }
      }
}
```

Ce message sera accessible dans le fichier tsx avec le nommage suivant:

```
this.i18n("partenairesListePage.form.titreFormulaire")
```

Ce qui donne dans le fichier :

```javascript
let intlMessages = this.i18n("partenairesListePage.form");
return (
    <div>
        <HornetForm
            form={this.state.form}
            titre={intlMessages.titreFormulaire}
            ...
```

### Formater un message complexe

#### A l'aide de la  méthode `i18n`


Exemple avec le message suivant:

```json
{
	partenaireFichePage: {
    	titre: "Gestion des partenaires - {nom} {prenom}"
  	}
}
```

Les variables "nom" et "prenom" sont valorisées comme suit:

```javascript

let titre = this.i18n(partenaireFichePage.titre, {
    nom: this.state.form.data.nom,
    prenom: this.state.form.data.prenom
});
...
```

## Les classes de chargement

Pour des besoins particuliers un projet peut créer sa propre classe de chargement, pour cela il suffit d'hériter de la classe `AbstractI18nLoader` de hornet-js-coresrc/i18n/abstract-i18n-loader.

### I18nLoader

Le constructeur prend en paramrètre le chemin contenant les différents fichiers de messages. Par défaut c'es le répertoire du point d'entrée suivi de `src\resources`.

### I18nLoaderSubDirectory

Le constructeur prend en paramètre un tableau contenant les répertoires de base pour la recherche des messages. Par défaut c'est initialisé avec le répertoire du point d'entrée suivi de `src\resources`.


