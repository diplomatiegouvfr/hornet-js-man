# Le composant Datasource

## Principe

Le composant `Datasource` permet de gérer une collection de données brutes.
Ces données peuvent provenir soit d'un service soit d'un tabeau de data.
L'objectif de ce composant est de donner une solution aux manipulations de données dans le maximum de cas
(initialisation, récupération, ajout, suppression, suppression totale, selection, tri, filtre, rechargement et gestion des erreurs)

Il existe 4 types de datasource:
- datasource "simple" : Fourni un ensemble de méthodes permettant la manipulation des données brutes.
- datasource "maitre - esclave" : caractéristiques datasource simple + une selection sur le master entraine une mise à jour de ses datasources esclaves.
- datasource "lié" :  caractéristiques datasource simple + capacité se connecter à un autre datasource lié. Cela permet de transferer une data d'un datasource à l'autre.
- datasource "paginé" : caractéristiques datasource simple + permet de réaliser une pagination côté client ou serveur.

### Principe technique

Ce composant propose des méthodes pour manipuler les données.
la plupart des méthodes déclenchent un évènement du même nom lorsque les traitements sont terminés.
Si une opération ne se déroule pas correctement, un évèment de type error est lancé avec
un numéro technique d'erreur (10301/10302/10303/10304/10305/10306).
Ce numéro indique le type d'erreur qui est survenue.

**Les méthodes datasource simple:**
Exemple :

```javascript

class ObjectMetier{
    constructor(public id : number, public label:string) {
    }
}

let datasource = new DataSource<ObjectMetier>([new ObjectMetier(1, "Allemagne"), etc...]);
```

- constructor:
new DataSource(DataSourceConfig | DataSourceConfigPage | Array<T>, keysMap : DataSourceMap = {}, options? : DataSourceOption[])
// avec new DataSourceConfig(scope : IService, methodName : string, fetchAttrName? : string) :  permet de définir un scope d'execution pour les datasources de type services.
// avec new DataSourceConfigPage(page : IHornetPage<any, any>, method : Function, fetchAttrName? : string)  :  permet de définir un scope d'execution pour les datasources de type services.
// avec  DataSourceMap un object simple de mapping exemple {"value" : "item.ville.id"}
// avec DataSourceOption permet de définir des options à un datasource. Ces options peuvent ou non être envoyées dans les arguments du fetch
Il est possible d'ajouter un tri par défaut dans les options, en définissant la clé à trier et la direction:

```javascript
let datasource = new PaginateDataSource<any>(new DataSourceConfigPage(this, this.getService().rechercher), {itemsPerPage: 10}, {}, [new DefaultSort([{
    key: "nom",
    dir: 0
} as SortData])]);
```

- on :
Méthode qui permet de s'enregistrer sur un événement (nom de l'événement passé en premier paramètre de la fonction) du datasource.

La méthode passée en paramètre sera appelée lorsque l'événement du datasource est envoyé.

exemple:

```javascript
dataSource.on("fetch", (Result)=>{
       //appelé lorsque le datasource envoie un évennement "fetch"
})
```

- fetch :
Méthode qui implémente la méthode de récupération des datas
Il est possible d'appliquer une transformation sur les données pour faire du mapping via un object de configuration :
{valeur : id, libelle : label }
par exemple

let datasource = new DataSource<ObjectMetier>([new ObjectMetier(1, "Allemagne"), etc...], {valeur : id, libelle : label });
[new ObjectMetier(1, "Allemagne"), etc...] => [{valeur : 1, libelle : "Allemagne"}, etc...]

Déchenche un event fetch

```javascript
dataSource.on("fetch", (Result)=>{
       //staff
})
dataSource.fetch(triggerFetch, args?, noSave?);
// triggerFetch  déclenche un évènement "fetch" après l'opération si true.
// args  paramètres à renseigner pour l'appel de la méthode de récupération des données.
// noSave indicateur pour sauvegarder ou non les paramètres du fetch pour les rejouer sur un sort service
```

- init :
Méthode qui déclenche un fetch appelé pour initialiser un datasource.
Déchenche un event init

```javascript
dataSource.on("init", (InitializedResult)=>{
       //staff
})
dataSource.init(args);
//args  paramètres à renseigner pour l'appel de la méthode de récupération des données.
//la methode init peut être couplée avec l'option InitAsync pour initialiser un datasource de façon asynchrone.
```

- add :
Ajout un élément ou des éléments au result du datasource
cette action déclenche l'évènement add.

```javascript
dataSource.on("add", (ProceedResult)=>{
       //staff
})
dataSource.add(triggerFetch, itemsToAdd);
// triggerFetch { Boolean } déclenche un évènement "fetch" après l'opération si true.
// ...items correspond aux données à ajouter, un appel à la méthode {@link DataSource#transformData} sera effectué

```

- delete :
enlève un élément ou des éléments au result du datasource
cette action déclenche l'évènement delete.

```javascript
dataSource.on("delete", (ProceedResult)=>{
       //staff
})
dataSource.delete(triggerFetch, itemsToDelete);
// triggerFetch { Boolean } déclenche un évènement "fetch" après l'opération si true.
// ...items correspond aux données à ajouter, un appel à la méthode {@link DataSource#transformData} sera effectué
```

- sort :
Renvoie le result du datasource trié en fonction du tableau de sortData ou de la fonction de compare.
cette action déclenche l'évènement sort.

```javascript
dataSource.on("sort", (SortedResult)=>{
       //staff
})
dataSource.sort(sortData[], compare? : Function);
//sortData données de tri, tableau object SortData.
```

Le tableau de sortData permet de proposer un tri multicolonne. Attention l'ordre des sortData dans le tableau est ici primordiale.
Si la fonction compare est definie c'est elle qui prend la main par rapport à la méthode de tri par defaut.
voici un exemple qui permet de trier les données d'un datasource en fonction des date de creation les plus récentes au plus ancienne d'abord
puis par ordre alphabetique sur la colonne des auteurs.

```javascript
dataSource.sort([ new SortData("dateCreat", SortDirection.DESC), new SortData("auteurCreat") ]);
```

- filter :
Renvoie un sous-ensemble des resultats filtrés
cette action déclenche l'évènement filter.

```javascript
dataSource.on("filter", (FilteredResult)=>{
       //staff
})
dataSource.filter(criteria, cancelFilterHistory?);
 //config correspond soit aux critères de filtrage soit à une fonction (appelée à chaque itération) {@link https://lodash.com/docs/#filter}
 //cancelFilterHistory false si on souhaite garder un historique des filtres true sinon. false par défaut
```

- select :
Permet de selectionner un element ou des elements du datasource
cette action déclenche l'évènement select.

```javascript
dataSource.on("select", (selectedItems)=>{
       //staff
})
dataSource.select(args);
//args correspond aux éléments sélectionnées
```

- reload :
On considère que les données sont dèjà présentes dans le datasource, on envoie juste l'event fetch
pour forcer le rendu avec ces données.

```javascript
dataSource.reload();
```

- deleteAll :
supprime toutes les données du datasource.

```javascript
dataSource.deleteAll();
```

- loadingData:
envoie un event lorsque le datasource effectue des opérations.
```javascript
dataSource.on("loadingData", (inProgress)=>{
       //staff
})
```

**Les méthodes datasourceMaster:**

Exemple :

```javascript

class ObjectMasterMetier{
    constructor(public id : number, public label:string) {
    }
}

class ObjectSubMetier{
    constructor(public id : number, public label:string) {
    }
}

let datasourceMaster = new DataSource<ObjectMasterMetier>([new ObjectMetier(1, "Allemagne"), etc...]);
let datasourceSlave =  new DataSource<ObjectSubMetier>(new DataSourceConfigPage(this, this.getService().getVille())
datasourceMaster.addSlave(this.datasourceSlave);
```

- addSlave :
Ajout d'un datasource
cette action déclenche l'évènement delete.

```javascript
datasourceMaster.addSlave(this.datasourceSlave);
```

- removeDatasource :
Ajout d'un datasource
cette action déclenche l'évènement delete.

```javascript
datasourceMaster.removeDatasource(this.datasourceSlave);
```

**Les méthodes datasourceLinked:**

Exemple :

```javascript

class Test{
       constructor (public id : number, public libelle : string, public nationalite : string){
       }
}

//utilisation avec un datasource de type tableau d'objets :
let dataSource1 = new DataSourceLinked<Test>(
            [new Test(1,"ob1","ob1"), new Test(2,"ob2","ob2")]);

let dataSource2 = new DataSourceLinked<Test>(
            [new Test(3,"ob3","ob3"), new Test(4,"ob4","ob4")]);

let dataSource3 = new DataSourceLinked<Test>(
            [new Test(5,"ob5","ob5"), new Test(6,"ob6","ob6")]);

dataSource1.connectTo(dataSource2).connectTo(dataSource3).connectTo(dataSource1);
```

- connectTo :
Permet de lier les datasources de type DataSourceLinked entre eux.

```javascript
dataSource1.connectTo(dataSource2).connectTo(dataSource3).connectTo(dataSource1)
```

- insert :
Ajout un élément ou des éléments au result à ce datasource et le supprime dans les autres datasources.
cette action déclenche l'évènement delete.

```javascript
dataSource1.insert(false, new Test(5,"ob5","ob5"));
```


**Les méthodes Paginatedatasource:**


Exemple de pagination coté serveur :

```javascript
 constructor(props?: HornetComponentProps, context?: any) {
    ...
    this.paginateDataSource = new PaginateDataSource<any>(new DataSourceConfigPage(this, this.getService().rechercher), {itemsPerPage: 10}, {});
    ...
 }
```

- goToPage :
Permet de naviguer vers une page

```xml

                <Table var="maTable" id="liste-partenaires">
                    <Header title={this.i18n("partenairesListePage.tableau.tableTitle")}>
                    </Header>
                    <Content dataSource={this.paginateDataSource}>
                        <Columns>
                            <CheckColumn keyColumn="id" title=""/>
                        </Columns>
                    </Content>
                    <Footer>
                        <Pager dataSource={this.paginateDataSource} id="maTable-paginate"/>
                    </Footer>
                </Table>

```

Exemple de pagination coté client :

```javascript

 constructor(props?: HornetComponentProps, context?: any) {
    ...
    this.paginateDataSource = new PaginateDataSource<any>([], {itemsPerPage: 10}, {});
    ...
 }

 prepareClient(): void {
    this.getService().rechercher(formData, MediaTypes.JSON).then((all)=>{
        this.paginateDataSource.add(false, all)
    })
 }
 
```

- goToPage :
Permet de naviguer vers une page

```xml

                <Table var="maTable" id="liste-partenaires">
                    <Header title={this.i18n("partenairesListePage.tableau.tableTitle")}>
                    </Header>
                    <Content dataSource={this.paginateDataSource}>
                        <Columns>
                            <CheckColumn keyColumn="id" title=""/>
                        </Columns>
                    </Content>
                    <Footer>
                        <Pager dataSource={this.paginateDataSource} id="maTable-paginate"/>
                    </Footer>
                </Table>
                

```

## Exemple simple

On possède pour notre rendu un tableau à alimenter grâce à des données reçu par le serveur.

```html

<div id="consulterPage">
    <h2>{this.i18n("produitsListePage.tableau.titre")}</h2>
    <Table var="maTable" id="liste-produits">
        <Content dataSource={this.dataSources}>
            <Columns>
                <Column keyColumn="id" title={this.i18n("produitsListePage.tableau.id")} sortable={false}/>
                <Column keyColumn="nom" title={this.i18n("produitsListePage.tableau.nom")} sortable={false}/>
                <Column keyColumn="desc" title={this.i18n("produitsListePage.tableau.description")} sortable={false}/>
            </Columns>
        </Content>
    </Table>
</div>

```

L'attribut à remplir pour alimenter automatiquement le tableau est le `*dataSource={this.dataSources}*`

Dans le contructeur de la page, on créer le dataSource (ici on notera le type ProduitMetier) :

```javascript

    constructor(props?: HornetComponentProps, context?: any) {
        super(props, context);
        // on créer le datasources
        this.dataSources = new DataSource<ProduitMetier>(null);

    }
    
```

Ma classe produit Metier : `src/models/pro/pro-mod.ts`

```javascript

export class ProduitMetier {
    id: number;
    nom: string;
    desc: string;
}

```

Enfin coté client on remplit notre objet `dataSource`.

```javascript

prepareClient(): void {
        this.getService().listerProduits(this.attributes.id).then((result : ProduitMetier[]) => {
            this.dataSources.add(true, result);
        })
    }
    
```
Ici, la fonction `listerProduits` retourne une promise qui est catché et associé à la variable `result` du type de mes données et ensuite est ajouté au `dataSource` avec le boolean de rafraichiement de la page à `true`. 