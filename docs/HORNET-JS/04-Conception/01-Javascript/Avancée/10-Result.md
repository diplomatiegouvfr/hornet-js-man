# Les Hornet Results

## Principe

Toutes les actions hornet côté back-end retournent des promesses de résultat.
Il suffit de passer ses données dans le bonne classe de resultat et la réponse sera configurée en conséquence
Il est possible de typer ces résultats avec les **HornetResults**.
Ces résultats permettent de configurer la réponse de la requête http.

Il en existe un certain nombre de types basiques :

* ResultFile : ResultHornet qui configure une réponse http de type fichier joint. C'est la classe parente de ResultBMP, ResultDOC, ResultCSV, ResultPDF et ResultOpenDocument.
* ResultStream : ResultHornet qui configure une réponse http de type stream.
* ResultBatch : ResultHornet qui configure une réponse http de type Batch (hornet-js-batch).
* ResultOpenDocument : ResultFile avec templating 'carbone.io'.

et ceux avec préconfiguration du mediaType :
* ResultJSON   : ResultHornet qui configure une réponse http de type JSON.
* ResultCSV    : ResultHornet qui configure une réponse http de type CVS.
* ResultJPG    : ResultHornet qui configure une réponse http de type JPG.
* ResultBMP    : ResultHornet qui configure une réponse http de type BMP.
* ResultPDF    : ResultHornet qui configure une réponse http de type PDF.
* ResultDOC    : ResultHornet qui configure une réponse http de type DOC.
* ResultODT    : ResultHornet qui configure une réponse http de type ODT.
* ResultODS    : ResultHornet qui configure une réponse http de type ODS.

Exemple 1 :
```javascript

export class ListerSecteurs extends RouteActionService<any, AdministrationSecteurService> {
    execute(): Promise<ResultJSON> {
        return this.getService().lister().then((data)=>{
            return new ResultJSON({data : data});
        });
    }
}

```

Important : Si le type de la promesse n'est pas typée un ResultJSON est crée automatiquement.
L'exemple 2 est strictement équivalent à l'exemple 1.

Exemple 2 :
```javascript

export class ListerSecteurs extends RouteActionService<any, AdministrationSecteurService> {
    execute(): Promise<any> {
        return this.getService().lister();
    }
}

```
## Les options des différentes classes de resultats

Chaque classe de type *Result* utilise des options qui lui permettent de fonctionner correctement.

### ResultFile
```javascript
{
    `data` : les données
    `filename` : le nom complet du fichier (avec extension)
    `encoding` : l'encodage du fichier
    `size` : la taille du fichier
    `dispositionType`: entête de réponse de disposition (`attachment` par défaut). De type DispositionType, une valeur à `DispositionType.INLINE` permet de laisser le navigateur gérer ca visualisation (sans fenêtre de téléchargement)
}
```

### ResultJSON
```javascript
{
    `data` : les données à envoyer dans la reponse
}
```

### ResultCSV
(voir https://www.npmjs.com/package/json2csv)

```javascript
{
    `fields` : les colonnes à ajouter dans la reponse
    `fieldNames` : le nom des colonnes
    `del` : le séparateur
    `defaultValue` : la valeur par defaut si le champ est vide
    `quotes` : quotes
    `doubleQuotes` : doubleQuotes
    `hasCSVColumnTitle` : indique si les noms des colonnes doivent être affichés
    `eol` : chaine de caractères ajouter sur chaque ligne
    `newLine` : type de retour à la ligne
    `flatten` : normalise un Json par défaut.
    `unwindPath` : permet de creer plusieurs lignes à partir d'un seul json
    `excelStrings` ; normalise les data pour excel
    `includeEmptyRows` : ne pas ignorer les lignes vides
    `data` : les données
    `filename` : le nom du fichier
    `encoding` : l'encodage du fichier
}
```

### ResultJPG
```javascript
{
    `data` : les données
    `filename` : le nom complet du fichier (avec extension)
    `encoding` : l'encodage du fichier
    `size` : la taille du fichier
    `dispositionType`: entête de réponse de disposition (`attachment` par défaut). De type DispositionType, une valeur à `DispositionType.INLINE` permet de laisser le navigateur gérer la visualisation (sans fenêtre de téléchargement)
}
```

### ResultBMP
```javascript
{
    `data` : les données
    `filename` : le nom complet du fichier (avec extension)
    `encoding` : l'encodage du fichier
    `size` : la taille du fichier
    `dispositionType`: entête de réponse de disposition (`attachment` par défaut). De type DispositionType, une valeur à `DispositionType.INLINE` permet de laisser le navigateur gérer la visualisation (sans fenêtre de téléchargement)
}
```

### ResultCSV
(voir https://github.com/bpampuch/pdfmake)

Exemple 3 :
```javascript

new ResultPDF({
                        data : retourApi.liste ,
                        fields : ["nom", "prenom", "organisme", "proCourriel"],
                        fieldNames : ["Nom", "Prénom", "Organisme", "Courriel"],
                        definition : {
                                pageSize: "A4",
                                content: [
                                {text: "Liste des partenaires", style: "subheader"},
                                {text: "Recherche par date de début : " + DateUtils.formatInTZ(retourApi.listeCriteres.startDate, DateUtils.YMD_Formats[0])},
                                {
                                    style: "tableExample",
                                    table: {headerRows: 2},
                                    layout: {
                                        fillColor: (i, node) => { return (i % 2 === 0) ?  "#F3F6F8" : null; }
                                    }
                                }],
                                header: {
                                    columns: [
                                        {alignment: "right",text : ""+ DateUtils.formatInTZ(new Date(), DateUtils.YMD_Formats[0])}
                                    ]
                                 },
                                footer: (page, pages) => {
                                    return {
                                        columns: [
                                            {
                                                alignment: "left",
                                                text : "footer left"
                                            },{
                                                alignment: "right",
                                                text: [
                                                    " Page ",
                                                    { text: page.toString() },
                                                    " sur ",
                                                    { text: pages.toString()}
                                                ]
                                            },
                                        ],
                                        margin: [10, 0]
                                    };
                                },
                                styles: {
                                    tableExample: {
                                        margin: [2, 2, 2, 2]
                                    },
                                    tableHeader: {
                                        bold: true,
                                        fontSize: 13,
                                        fillColor: "#8FAFCC"
                                    },
                                    subheader :{
                                        margin: [20, 20, 20, 20],
                                        bold: true,
                                        fontSize: 25,
                                        alignment: "center"
                                    }
                                }
                            }} as OptionsPDF);

```

### ResultDOC
```javascript
{
    `data` : les données
    `filename` : le nom complet du fichier (avec extension)
    `encoding` : l'encodage du fichier
    `size` : la taille du fichier
    `dispositionType`: entête de réponse de disposition (`attachment` par défaut)
}
```

### ResultBatch
```javascript
{
    `data` : les données à envoyer dans la réponse
}

### ResultStream
```javascript
{
    `data` : les données à envoyer dans la réponse,
    `mimeType` : le mime type du fichier
}
```

Pour le ResultStream il faut utiliser le fetchOnStream pour la partie service:
```javascript
rechercher(criteres: PartenaireRechercheParameter, reqMimeType: MediaType, res?: NodeJS.WritableStream): Promise<any> {
    logger.trace("SERVICES - rechercher : ", criteres, reqMimeType);
    let request: HornetRequest = { url: this.buildUrl(URL_PARTENAIRES + URL_PAR_RECHERCHE), method: "post", data: criteres, typeMime: reqMimeType };
    return (res) ? this.fetchOnStream(request, res) : this.fetch(request);
}
```

### ResultOpenDocument
(se référer à la documentation officielle : https://github.com/Ideolys/carbone)

```javascript
    {
        /** Fichier template utilisé pour la génération */
        templateFilePath: string;
        /** Options pour carbone.io */
        computeOptions: Object;
    }
```