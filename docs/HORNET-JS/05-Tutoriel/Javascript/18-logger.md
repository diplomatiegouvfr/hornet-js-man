# Logger

hornet-js-utils met à disposition une déclaration et utilisation de log simplifiée au travers d'un decorator @logger(). Celui-ci s'appuie sur le composant `Logger` de `hornet-js-utils`.

## Le decorator
`@logger()` est un décorateur de méthode qui peut prendre en paramètre un objet d'options de type `LoggerOptions` tel que :

| Attribut        | Description                                                           | Obligatoire | Valeur par défaut   | Type       |
|-----------------|-----------------------------------------------------------------------|-------------|---------------------|------------|
| level           | Niveau de log souhaité                                                | Non         | LogLevels.INFO      | LogLevels  |
| identification  | Arborescence et nom du fichier                                        | Non         | filepath et méthode | string     |
| message         | Objet permettant l'utilisation d'un template                          | Non         | -                   | Object     |
| template        | Message templétisé                                                    | Non         | -                   | string     |
| values          | Objet contenant les valeurs à utiliser dans le template               | Non         | this                | Object     |
| remplaceUndef   | Symbole à positionner dans le template si valeur attentue non trouvée | Non         | ?                   | string     |

```
export interface LoggerOptions {
    level?: LogLevels;
    identification?: string;
    message?: {
        template: any;
        values?: any;
        remplaceUndef?: string;
    }
}
```

### Cas d'utilisations

#### Cas par défaut

Par défaut le logger utilise un niveau de log `info`, indique le filepath et le nom de la méthode sur laquelle il est positionné

```
import logger from "hornet-js-utils/src/logger/dec-logger";

class myClass {
    @logger()
    render() {

    }
}
```

#### Cas d'utilisation le plus complet

```
import logger, { LogLevels } from "hornet-js-utils/src/logger/dec-logger";

class myClass {
    @logger({
        level: LogLevels.ERROR,
        identification: "hornet-js-man/docs/HORNET-JS/05-Tutoriel/Javascript/18-logger",
        message: {
            template: "Log applicative ERROR : ${subObject.id} ${subObject.name}",
            values: myObject,
            remplaceUndef: "?"
        }})
    render() {

    }
}
```