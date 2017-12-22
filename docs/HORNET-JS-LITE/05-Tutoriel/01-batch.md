# Batch

Projet hornet-js-batch

## Objectif

Pouvoir lancer des BATCHS sur le backend via des urls.

## Principe
Les batchs hornet s'articulent principalement autour des `RouteAction` d'hornet.
C'est le même principe que pour les routes de service d'hornet:
on définit une route lazy dans le fichier des routes de son application batch puis on définit une action dans le repertoire `action`.

Pour faire un batch, il suffit que votre action hérite de la classe RouteActionBatch.
La méthode `execute` de l'action renvoie une promesse de type ResultBach.

Ce type d'action propose une méthode pour créer une unité de traitement getNewBatchUnit() qui prend en paramètre le modèle de données (paramètre optionnel).

Cette méthode renvoie une nouvelle instance de BatchUnit qui propose un certain nombre de méthodes (reader, filter, transform, mapper, writer, call, foreach, run).

On peut regrouper en 3 groupes ces méthodes :

- celles qui renvoient leur résultat au traitement suivant : `reader`, `filter`, `mapper`, `transform`

- celles qui renvoient leurs propres arguments au traitement suivant : `writer`, `call`, `foreach`

- et `run` qui est permet de lancer l'unité de traitement.


`reader` : ajoute un ou plusieurs readers dans le processus du batchunit. Les `readers` sont des classes de type BatchReader qui vont récupérer des données.

`filter` : ajoute un filter dans le processus du batchunit. Les `filters` sont des classes de type BatchFilter qui vont filtrer les données.

`mapper` : ajoute un mapper dans le processus du batchunit. Les `mappers` sont des classes de type BatchMapper qui vont binder les données.

`transform` : ajoute un transform dans le processus du batchunit. Les `transformers` sont des classes de type BatchTransform qui vont manipuler les données.

`writer` : ajoute un ou plusieurs writers dans le processus du batchunit. Les `writers` sont des classes de type BatchWriter qui vont renvoyer les données.

`call` : ajoute un appel de service dans le processus du batchunit. Les `call` sont des classes de type BatchService qui vont executer l'appel de service.

`foreach` : ajoute un appel de service dans le processus du batchunit. Les `foreach` sont des classes de type BatchService qui vont executer l'appel de service en bouclant sur la liste des paramètres qu'ils ont en entrée.

### Exemple d'utilisation

```javascript
import { RouteActionBatch } from "hornet-js-core/src/routes/abstract-routes";
import { CSVReader } from "hornet-js-core/src/batch/reader/csv-reader";
import { ServiceReader } from "hornet-js-core/src/batch/reader/service-reader";
import { ResultBatch } from "hornet-js-core/src/result/result";
import { AdministrationSecteurService } from "src/services/page/adm/adm-secteur-service-page";
import { SecteurMetier } from "src/models/adm/sec-mod";

export class CreerSecteurBatch extends RouteActionBatch<any, AdministrationSecteurService> {
    execute(): Promise<ResultBatch>  {

        let unit = this.getNewBatchUnit()
                .reader(new ServiceReader(this.getService().lister, this))
                .filter((item) => {
                   return item.desc=="secteurBatch";
                 })
                .transform((result : Array<any>)=>{
                    result.forEach((value, index)=>{value.desc +="test";});
                    return result;
                })
                .foreach(this.getService().creer, this)
                .run();

        return unit.then((result)=>{
            return new ResultBatch({data : result});
        })
    }
}
```

## Configuration printer

Définit un interval d'affichage dans les logs du batch de l'état d'avancement.

Valeur par défaut : `500`

```json
  "batch": {
    "printTimer": 15000
  }
```

### Lancement du script Auto_RunJob.sh
```shell
./Auto_RunJob.sh  localhost 8889 secteurs/batch/1

```
