# Les Hornet Results

## Principe

Toutes les actions hornet coté back-end retournent des promesses de resultat.
Il suffit de passer ses data dans le bon result et la reponse sera configurée en conséquence
Il est possible de typer ces resultats avec les hornetResults.
Ces results permettent de configurer la reponse de la requête http.

il existe un certain nombre de result:
ResultJSON   : ResultHornet qui configure une réponse http de type JSON.
ResultCSV    : ResultHornet qui configure une réponse http de type CVS.
ResultJPG    : ResultHornet qui configure une réponse http de type JPG.
ResultBMP    : ResultHornet qui configure une réponse http de type BMP.
ResultPDF    : ResultHornet qui configure une réponse http de type PDF.
ResultDOC    : ResultHornet qui configure une réponse http de type DOC.
ResultBatch  : ResultHornet qui configure une réponse http de type Batch.
ResultODT    : ResultHornet qui configure une réponse http de type ODT.
ResultODS    : ResultHornet qui configure une réponse http de type ODS.
ResultStream : ResultHornet qui configure une réponse http de type stream.


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

** Les options des différents results : **

Chaque result utilise des options qui lui permettent de fonctionner correctement

-Option disponible pour ResultJSON : {
                                        `data` : les données à envoyer dans la reponse
                                     }

-Option disponible pour ResultCSV (voir https://www.npmjs.com/package/json2csv): {
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

-Option disponible pour ResultJPG : {
                                        `data` : les données
                                        `filename` : le nom du fichier
                                        `encoding` : l'encodage du fichier
                                        `size` : la taille du fichier
                                    }

-Option disponible pour ResultBMP : {
                                        `data` : les données
                                        `filename` : le nom du fichier
                                        `encoding` : l'encodage du fichier
                                        `size` : la taille du fichier
                                    }

-Option disponible pour ResultPDF : (voir https://github.com/bpampuch/pdfmake)

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
-Option disponible pour ResultDOC : {
                                        `data` : les données
                                        `filename` : le nom du fichier
                                        `encoding` : l'encodage du fichier
                                        `size` : la taille du fichier
                                    }

-Option disponible pour ResultBatch : {
                                          `data` : les données à envoyer dans la réponse
                                       }

-Option disponible pour ResultStream : {
                                           `data` : les données à envoyer dans la réponse,
                                           `mimeType` : le mime type du fichier
                                        }
Pour le ResultStream il faut utiliser le fetchOnStream pour la partie service:
```
rechercher(criteres: PartenaireRechercheParameter, reqMimeType: MediaType, res?: NodeJS.WritableStream): Promise<any> {
    logger.trace("SERVICES - rechercher : ", criteres, reqMimeType);
    let request: HornetRequest = { url: this.buildUrl(URL_PARTENAIRES + URL_PAR_RECHERCHE), method: "post", data: criteres, typeMime: reqMimeType };
    return (res) ? this.fetchOnStream(request, res) : this.fetch(request);
}
```
-Options disponibles pour ResultOpenDocument (ODT et ODS) :

```javascript
    {
        /** Fichier template utilisé pour la génération */
        templateFilePath: string;
        /** Options pour carbone.io */
        computeOptions: Object;
    }
```

pour les options Carbone.io, se référer à la documentation officielle : https://github.com/Ideolys/carbone
