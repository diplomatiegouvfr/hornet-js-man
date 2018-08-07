# Injection dans l'application

Afin de répondre à des contraintes de traitement différent suivant le côté où le code est exécuté (client ou serveur), nous avons crée un injecteur.

Ce dernier peut gérer des Objets automatiquement ou manuellement.

## Decorateurs

Pour la gestion automatique, nous avons la possibilité d'utiliser des décorateurs pour ajouter notre objet dans l'injecteur, et pour pouvoir en injecter dans un constructeur.

### injectable

Ce décorateur est appliquable sur une classe :

```javascript
import { injectable } from "hornet-js-core/src/inject/injectable";
import { BeanToInject, HOW_I_AM } from "test/inject/bean";

@injectable(BeanToInject)
export class AutoBeanToInject {
    public howIAm():string {
        return HOW_I_AM;
    }
}
```

Il permet d'ajouter cette valeur (la classe) dans l'injecteur.

paramètres :

| nom   | facultatif | type                                               | Description                                                             |
|-------|------------|----------------------------------------------------|-------------------------------------------------------------------------|
| key   | &nbsp;     | Class<any> &#124; AbstractClass<any> &#124; string | Clé de d'enregistrement dans l'injecteur.                               |
| scope | &nbsp;     | Scope                                              | Scope de la valeur à insérer dans l'injecteur (valeur par défaut VALUE) |
| side  | X          | Side                                               | Si rien n'est précisé il met celui en cours à l'exécution (CLIENT si on exécute le code côté client) |

### inject

Ce décorateur est appliquable sur un paramètre de constructeur d'une classe qui devra être annotée avec `@injectable`:

```javascript
import { BeanInject, BeanToInject } from "test/inject/bean"
import { injectable, Side } from "hornet-js-core/src/inject/injectable";
import { inject } from "hornet-js-core/src/inject/inject";
import { Class } from "hornet-js-utils/src/typescript-utils";

@injectable(BeanInject, undefined, Side.SERVER)
export class BeanServerInject extends BeanInject {
    bean:BeanToInject

    constructor(@inject(BeanToInject) bean:BeanToInject) {
        super();
        this.bean = bean;
    }

    howIAm():string {
        return this.bean.howIAm();
    }

}
```
paramètres :

| nom | facultatif | type | Description |
|-----|------------|------|-------------|
| key | &nbsp;     | Class<any> &#124; AbstractClass<any> &#124; string | Clé de récupération dans l'injecteur. |

Le décorateur `@injectable` surcharge le constructeur si un des paramètres est annoté avec `@inject`, et interroge l'injecteur, pour récupérer la valeur à injecter avant d'appeler le constructeur surchargé.

## Scope et Side

Scope :

|   valeur  | Description |
|-----------|-------------|
| PROTOTYPE | Pour récupérer une nouvelle instance d'une classe à chaque récupération dans l'injecteur. |
| SINGLETON | Pour récupérer la même instance d'une classe à chaque récupération dans l'injecteur. |
| VALUE     | Enregistre la valeur telle quelle dans l'injecteur (La classe pour une classe) . |

Side :

| valeur |
|--------|
| SERVER |
| CLIENT |


## Injector

Cette classe gère le registre d'injection (ajout, récupération et suppression d'une valeur)

### Enregistrement d'une valeur

La méthode à appeler est `register(key, value, scope, side)` :

paramêtres :

| nom   | facultatif | type                                               | Description                                                             |
|-------|------------|----------------------------------------------------|-------------------------------------------------------------------------|
| key   | &nbsp;     | Class<any> &#124; AbstractClass<any> &#124; string | Clé de d'enregistrement dans l'injecteur.                               |
| value | &nbsp;     | any                                                | Valeur associé à la clé.                                                |
| scope | &nbsp;     | Scope                                              | Scope de la valeur à insérer dans l'injecteur (valeur par défaut VALUE) |
| side  | X          | Side                                               | Si rien n'est préciser il met celui en cours à l'exécution, sinon test si le Side correspond et ne l'ajoute pas dans le registre en cas de non correspondance |

Si la clé est une classe, on ajoute en dynamique une ID d'injection unique sur cette dernière, et c'est celui-ci qui servira d'ID dans le registre.  

### Récupération d'une valeur

La méthode à appeler est `getRegistered(key, side)` 

paramètres :

| nom  | facultatif | type                                               | Description                               |
|------|------------|----------------------------------------------------|-------------------------------------------|
| key  | &nbsp;     | Class<any> &#124; AbstractClass<any> &#124; string | Clé de d'enregistrement dans l'injecteur. |
| side | X          | Side                                               | Si rien n'est préciser il met celui en cours à l'exécution (CLIENT si on exécute le code coté client) |

Si la clé correspond à une classe, lors de l'enregistrement on a affecté une propriété static à la classe. c'est cette dernière qu'on utilise pour récupérer la valeur dans le registre.

### Suppression d'une valeur

La méthode à appeler est `removeRegistered(key, side)` 


| nom  | facultatif | type                                               | Description                                             |
|------|------------|----------------------------------------------------|---------------------------------------------------------|
| key  | &nbsp;     | Class<any> &#124; AbstractClass<any> &#124; string | Clé de d'enregistrement dans l'injecteur.               |
| side | X          | Side                                               | Si rien n'est préciser il met celui en cours à l'exécution (CLIENT si on exécute le code coté client) |

Comme pour la récupération, si la clé correspond à une classe, lors de l'enregistrement on a affecté une propriété static à la classe. c'est cette dernière qu'on utilise pour récupérer la valeur dans le registre.

### Exemples

// ajoute dans le registre d'injection la classe BeanBoth, aassocié à la clé Bean (classe abstraite)
Injector.register(Bean, BeanBoth, Scope.VALUE);

// Récupère dans le registre d'injection l'objet aassocié à la clé Bean (classe abstraite)
Injector.getRegistered(Bean)

// ajoute dans le registre d'injection une instance de la classe BeanBoth, aassocié à la clé Bean (classe abstraite)
Injector.register(Bean, BeanBoth, Scope.SINGLETON, Side.CLIENT);

// Supprime du registre d'injection l'objet associé à la clé Bean (classe abstraite)
Injector.removeRegistered(Bean);

// ajoute dans le registre d'injection une instance de la classe BeanBoth, aassocié à la clé Bean (classe abstraite)
// identique au Scope.SINGLETON 
Injector.register(BeanToInject, new BeanToInject());
