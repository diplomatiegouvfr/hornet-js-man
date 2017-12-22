# Socle technique

## Déclaration des ressources JNDI

JNDI signifie Java Naming and Directory Interface, cette API permet :

- d’accéder à différents services de nommage ou de répertoire de façon uniforme ;
- d'organiser et rechercher des informations ou des objets par nommage (Java Naming and Directory Interface) ;
- de faire des opérations sur des annuaires (Java Naming and Directory Interface)

JNDI est très utilisée dans l'univers des serveurs d'applications Java et fait partie de l'ensemble des APIs Java EE où il permet de lier un nom (par exemple `base/sql/login`) à une information.

Les ressources peuvent être de deux natures différentes :

- Ressource locale

    - `<Resource>` enfant de l'élément `<Context>`
    - name : permet de ranger la ressource dans l'arbre JNDI
    - auth : prend pour valeur « Container » ou « Application » pour désigner qui de Tomcat ou du programmeur doit fournir les accréditations pour accéder à la ressource
    - type : la classe Java qui détermine le reste de la configuration de la ressource

- Ressource globale

    - `<Resource>` enfant de l'élément `<Server>/<GlobalNamingResources>`
    - La configuration se fait comme pour une ressource locale
    - Dans le contexte : <ResourceLink>
    - type : reprise de celui de la ressource globale
    - name : nom JNDI accessible par l'application
    - global : référence au nom utilisé dans la partie globale

Exemple de ressources :

- JDBC Datasource
- JavaMail Session
- Custom Object Factory
- …

Les sources de données sont gérées comme des sources locales et sont à définir dans `META-INF/context.xml`

```xml
    <Context path="/PROJET" reloadable="true">
            ...
    	<Resource name="jdbc/demoGlobal" type="javax.sql.DataSource"  
            auth="Container" username="root" password="root"
            driverClassName="net.sourceforge.jtds.jdbc.Driver"
            url="jdbc:jtds:sqlserver://localhost:1433/DEMO" initialSize="2"
            maxActive="9" maxIdle="4" minIdle="2" maxWait="5000" />
    </Context>
```

Le nœud `Context` contient les caractéristiques suivantes :

- `path` : discriminant dans l'url qui identifie l'application
- `reloadable` : scrutation des répertoires classes et lib pour recharger le contexte dynamiquement en cas de modifications.


## Gestion des erreurs


### Erreurs techniques

Concerne les erreurs non fonctionnelles, non prévues et souvent irrécupérables du type rupture de communication réseau (Apache, Navigateur Web, Base de de donnée), perte de session, `file not found`, `out of memory`, `null pointer`, et les `RuntimeException` en général.

Le framework génère automatiquement  un rapport d’erreur détaillé dans les logs, et un message simplifié destiné à l’utilisateur incluant un identifiant unique de l’erreur produite (au format timestamp).

###	Erreurs fonctionnelles

Les erreurs fonctionnelles concernent le métier de l’application, elles doivent donc remonter à l’utilisateur avec un niveau de détail élevé.

Il est possible de lever une erreur fonctionnelle soit dans la couche métier, soit web. Pour cela il suffit d’ajouter le code :

```java
    throw new BusinessException("monCodeErreur", new String[]{argument1, argument2, …});
```

Cette exception remonte la pile d’exécution pour être interceptée par la couche web, qui se charge de transmettre un message clair avec le [gestionnaire d'erreurs](#gestionnaire_erreur) pour l’utilisateur.


## Gestion des logs

- Utilisation de Logback.  
  Le code ne doit pas référencer directement Logback mais doit toujours passer par l'interface SLF4J.
- Paramétrage dans `/src/main/resources/logback.xml`

## Package sun.*

### Enlever accent

La méthode du framework `StringUtils.enleverAccent` permet de remplacer la classe `sun.text.Normalizer` du jdk 5 qui est devenu obsolète dans la version 6.

Exemple d’utilisation :

```java
    String chaine = "abàààààcdefghijklmnopqrstuvwxyz0123456789éèêàâ";
    String chaineResultat = "abaaaaacdefghijklmnopqrstuvwxyz0123456789eeeaa";

    String result = StringUtils.enleverAccent(chaine);
```

## Gestion des dépendances

### Principe

Les dépendances sont gérées par Maven qui s'articule autour de fichiers de configuration `pom.xml`.

Chaque module Maven contient à la racine de son dossier, un fichier `pom.xml`

Un module Maven est identifié par les 3 clés suivantes :

- Son `groupId` : le nom de package / organisation qui maintient le module
- Son `artifactId` : le nom du module au sein du package / organisation
- Sa `version` : le numéro de la version du module

### Types des modules

Un module maven peut être de différent type :

- `jar` : indique que le module génère un jar, qui pourra lui même être utilisé comme dépendance d'un autre module maven
- `war` : indique que le module génère une application web
- `pom` : indique que le module ne génère pas de livrable. Ce type est souvent utilisé pour déclarer un module __parent__, parent d'autres modules. Il est alors possible d'y factoriser de la configuration, qui s'appliquera à tous les modules fils.

### Configuration d'un module maven

Les modules Maven, via leur fichier de configuration `pom.xml`, déclarent les dépendances dont ils vont avoir besoin.

Les dépendances sont en fait d'autres modules maven de type `jar`.

Lors du packaging d'une application web (type `war`), Maven va résoudre l'intégralité de l'arbre de dépendances et en déduire tous les jars à embarquer dans le war.

Exemple de déclaration de dépendance dans le `pom.xml` :

```xml
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.2.8</version>
    </dependency>
```

#### Réutilisation des dépendances du framework (notion de BOM)

Le framework Hornet déclare et utilise déjà un certain nombre de dépendances.

Si une application utilisant le framework Hornet souhaite également utiliser une de ces dépendances, il est vivement conseillé qu'elle ré-utilise la même version du module. Dans le cas contraire cela risque de créer des incompatibilités et des erreurs au runtime.

Afin de répondre à cette problématique, toutes les dépendances du framework ont été déclarées dans un module de type `pom` qui est importé à la fois par le framework, et à la fois par les applications utilisants le framework.

Par exemple : Logback, qui est utilisé et par le framework et par les applications Web, est déclaré de la sorte dans le BOM :

```xml
    <dependencyManagement>
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
            <version>1.1.2</version>
        </dependency>
    </dependencyManagement>
```

La dépendance est déclarée dans l'application Web et dans le framework de la manière suivante :

```xml
    <dependency>
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-classic</artifactId>
    </dependency>
```

__Note__ : La version n'est plus précisée à ce niveau, ainsi on est sûr d'avoir toujours la même version entre le framework et l'application.


#### Déclaration du parent

Tous les projets utilisant le framework Hornet doivent déclarer un parent de la manière suivante :

```xml
	<parent>
		<groupId>fr.gouv.diplomatie.hornet</groupId>
		<artifactId>hornet-service-parent</artifactId>
		<version>5.0.X</version>
	</parent>
```

Cela permet d'intégrer automatiquement certains paramétrages, notamment BOM présenté ci-dessus, au projet courant.

Le framework hornet propose d'autres pom parents plus spécifiques qui proposent des dépendances pour des applications de ce type.

- Le pom parent de type batch (application batch)
- Le pom parent de type orm (application orientée données)
- Le pom parent de type reports (application orientée rapports)

### Cache

Maven gère un repo local pour y stocker les modules Maven (aussi appelés `artifacts`) qui ont été installés.

Son emplacement par défaut est situé dans le répertoire de l’utilisateur : `~/.m2/`

## Gestion des exports ##

### Utilisation côté client ###

Le client accède à une ressource via l'API REST de manière classique.

Il peut indiquer le format de retour souhaité de deux manières :

- Avec le paramètre d'URL `mediaType`
Exemple :
```javascript
superagent.get("http://hornet-app/ressources?mediaType=csv")
```
- Avec la balise du header `Accept`
Exemple :
```javascript
superagent.get("http://hornet-app/ressources") //
    .set("Accept", "text/csv") //
```

Le paramètre d'URL est prioritaire sur le header `Accept`.

Un mapping est fait côté serveur entre la valeur du paramètre `mediaType`et le mime-type :

- `mediaType=xls` : `application/vnd.ms-excel`

- `mediaType=csv` : `text/csv`

- `mediaType=pdf` : `application/pdf`


#### Retour du serveur ####

Si le serveur est en mesure de fournir le format demandé, pour la ressource demandée, il renvoie un code HTTP 200 (OK), la balise `content-type` positionné au mime-type renvoyé, et le contenu du document dans le body.

Dans le cas contraire, un code HTTP 406 (Not Acceptable Response) est renvoyé avec un contenu vide.

#### Négociation de contenu ####

Le client peut "_négocier_" le type de retour, en :

- indiquant plusieurs formats qu'il peut accepter
- en précisant leur priorité

Pour plus de détails, voir [la norme HTTP](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

Exemple : `Accept:text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8`

>Article interessant sur le sujet : http://www.newmediacampaigns.com/blog/browser-rest-http-accept-headers

### Fonctionnement côté serveur ###

#### Compilation Jasper Reports via Maven ####

Le pom parent (hornet-service-parent) fournit un plugin de compilation maven pour Jasper Reports. Il permet de compiler automatiquement les rapports Jasper au format JRXML vers un format binaire (.jasper). L'exécution du plugin se déclenche sur la phase compile (goal jasper). Pour l'ajouter, il suffit de déclarer le plugin dans le pom projet, à l'intérieur de la section build\plugins :

```xml
	<!-- jasperreports-plugin -->
	<plugin>
	    <groupId>com.alexnederlof</groupId>
	    <artifactId>jasperreports-plugin</artifactId>
	</plugin>
```

Par défaut, le plugin utilise le moteur de compilation Jasper 6.2.0. Il va lire les fichiers .jrxml dans le répertoire src/main/resources/jasperreports, et écrit les fichiers .jasper dans le répertoire target/jasper. Les éléments de configuration peuvent être rédéfinis au besoin à la déclaration du plugin :

```xml
	<!-- jasperreports-plugin -->
	<plugin>
	    <groupId>com.alexnederlof</groupId>
	    <artifactId>jasperreports-plugin</artifactId>
	    <configuration>
	        <compiler>net.sf.jasperreports.engine.design.JRJdtCompiler</compiler>
	        <sourceDirectory>src/main/jasperreports</sourceDirectory>
	        <outputDirectory>${project.build.directory}/jasper</outputDirectory>
	        <outputFileExt>.jasper</outputFileExt>
	        <xmlValidation>true</xmlValidation>
	        <verbose>false</verbose>
	        <numberOfThreads>4</numberOfThreads>
	        <additionalProperties>
	        	<net.sf.jasperreports.default.pdf.encoding>UTF-8</net.sf.jasperreports.default.pdf.encoding>
	        </additionalProperties>
	    </configuration>
	    <dependencies>
	    	<!-- changer la version de jasper si nécessaire -->
	    </dependencies>
	</plugin>
```

A la racine sous `<configuration>`, il s'agit de la configuration du plugin proprement dit. L'élément verbose permet notamment d'afficher les détails de la compilation, afin de déboguer les éventuels problèmes. Attention, il existe un bug avec le paramètre xmlValidation=false.

Sous `<additionalProperties>`, ce sont les paramètres du moteur de compilation/exécution Jasper, qu'il est parfois nécessaire de redéfinir. La liste des paramètres disponibles pour la version 6.2.0 est documentée en ligne à [cette adresse] (http://jasperreports.sourceforge.net/config.reference.html)

Remarque : le plugin jasperreports-plugin configure sa propre dépendance vers jasper-reports (afin de forcer la version 6.2.0). Or, cette dépendance est également déclarée dans le bom (hornet-service-bom). Pour éviter les conflits, le numéro de version est le même. Si le projet a besoin de changer la version de jasper au niveau du plugin, il faut faire le nécessaire pour résoudre les conflits (par exemple, mettre la même version partout lorsque c'est possible, et sinon exclure jasper des autres dépendances).


#### Fonctionnement Spring Web-MVC ####

La négociation de contenu de Spring se base sur des objets de type  `HttpMessageConverter`.

Un `HttpMessageConverter` est caractérisé par :

- Les mime-types qu'il supporte
- Les classes qu'il supporte

Spring dispose nativement d'un certains nombre de `HttpMessageConverter`, sachant gérer notamment les formats :

- JSON
- XML
- RSS
- etc...

Pour plus de détails, voir la [documentation complète](http://docs.spring.io/spring-framework/docs/4.1.4.RELEASE/spring-framework-reference/html/mvc.html#mvc-config-enable).

Ces `HttpMessageConverter` gèrent les conversions d'objets en entrées (request) et en sortie (response).

- En entrée :
Lorsqu'une requête arrive, Spring parcourt les différents `HttpMessageConverter` disponibles et retient celui qui gère le format demandé en entrée par la méthode du controleur.
Le `HttpMessageConverter` est alors chargé de désérialiser le contenu de la requête dans l'objet en entrée.

- En sortie :
Lorsqu'une réponse est sur le point d'être envoyée, Spring parcourt les différents `HttpMessageConverter` disponibles et retient celui qui gère le format préféré par le client.
Le `HttpMessageConverter` est alors chargé de sérialiser l'objet de sortie de la méthode du controleur, dans le flux de réponse.


#### Fonctionnement Framework Hornet ####

Côté framework, le mécanisme d'export est basé sur 2 couches :

- Un `AbstractHornetHttpMessageConverter` :
    - Il définit le mime-type accepté
    - Il implémente l'écriture dans le flux de réponse, à partir d'un objet `model` spécifique au mime-type
    - Il délègue la construction du `model` (spécifique à __une__ classe) à des sous services
- Une liste de services _délégués_ à ce convertisseur, qui définissent chacun __une__ classe Java gérée

Pour que Spring confie la sérialisation d'un objet à un `AbstractHornetHttpMessageConverter`, il faut que ce dernier supporte le mime-type demandé, __et__ qu'il dispose d'un délégué gérant la classe à sérialiser.

#### Mise en place dans le Framework Hornet ####

Le framework Hornet ajoute par défaut 3 nouveaux `AbstractHornetHttpMessageConverter` :

- `XlsHttpMessageConverter` :
    - Gère le mime-type `application/vnd.ms-excel`
    - Délègue la création du model aux services de type `ExportXlsModelService`
    - Implémente l'écriture d'un objet POI dans le flux de réponse
- `CsvHttpMessageConverter` :
    - Gère le mime-type `text/csv`
    - Délègue la création du model aux services de type `ExportCsvModelService`
    - Implémente l'écriture d'un objet `TableVO`dans le flux de réponse
- `PdfHttpMessageConverter` :
    - Gère le mime-type `application/pdf`
    - Délègue la création du model aux services de type `ExportPdfModelService`
    - Implémente l'écriture d'un objet JasperReports dans le flux de réponse



### Utilisation dans l'application ###

#### Ajouter un export natif framework (PDF, XLS ou CSV) ####

Ce paragraphe explique l'utilisation du mécanisme d'export via l'exemple suivant :

Ajout d'un export `xls` de la classe `Partenaire` sur la méthode REST de lecture de `Partenaire`.

##### Ajout du service d'export #####

- Créer une classe `PartenaireXlsExportService` qui implémente `ExportXlsModelService<Partenaire>`.

	Note :
	- En implémentant `ExportXlsModelService`, le service sera automatiquement vu par le `XlsHttpMessageConverter`.
	- Il est possible de mutualiser plusieurs exports dans une seule classe en implémentant plusieurs interfaces.

- Implémenter la méthode `supports` pour répondre `true` lorsqu'on lui donne en paramètre un objet `Partenaire`
- Implémenter la méthode `construireXlsModel` pour renvoyer le model Excel POI valorisé avec les données de `Partenaire` (non détaillé ici, voir la documentation POI)
- Ajouter l'annotation `@Service` pour ajouter le service au contexte Spring

Code du service créé :

```java
@Service
public class PartenaireXlsExportService implements ExportXlsModelService<Partenaire> {

    @Override
    public boolean supports(final Class<?> clazz) {

        return Partenaire.class.isAssignableFrom(clazz);
    }

    @Override
    public HSSFWorkbook construireXlsModel(final Partenaire toExport) {

        final HSSFWorkbook model = new HSSFWorkbook();
        // {...}
        return model;
    }

}
```

##### Configuration du Controleur Web #####

Maintenant que Spring dispose des éléments nécessaires pour exporter un objet `Partenaire` en fichier excel, il ne reste plus qu'à modifier le controleur pour autoriser ce format de sortie.

Code source initial du controleur :

```java
@RestController
@RequestMapping(value = "/partenaires", produces = {MediaType.APPLICATION_JSON_VALUE})
public class PartenaireController {

    private static final Logger LOG = LoggerFactory.getLogger(PartenaireController.class);

    @Resource
    private PartenaireService partenaireService;

    @RequestMapping(value = "/{id}", method = RequestMethod.GET)
    public Partenaire lire(@PathVariable final Long id) {

        final Partenaire partenaire = partenaireService.lirePartenaire(id);
        return partenaire;
    }
}
```

L'annotation de __classe__ `@RequestMapping(..., produces = {MediaType.APPLICATION_JSON_VALUE})` indique que par défaut, toutes les méthodes ne renvoient que du JSON.

Il suffit d'écraser cette annotation pour la méthode `lire`, en ajoutant le type _excel_ :

```java
@RestController
@RequestMapping(value = "/partenaires", produces = {MediaType.APPLICATION_JSON_VALUE})
public class PartenaireController {

    private static final Logger LOG = LoggerFactory.getLogger(PartenaireController.class);

    @Resource
    private PartenaireService partenaireService;

    @RequestMapping(value = "/{id}", method = RequestMethod.GET, produces = {
        MediaType.APPLICATION_JSON_VALUE,
        HornetMediaType.APPLICATION_EXCEL_VALUE})
    public Partenaire lire(@PathVariable final Long id) {

        final Partenaire partenaire = partenaireService.lirePartenaire(id);
        return partenaire;
    }

}
```

#### Ajouter un nouveau format d'export ####

##### Ajout d'un nouveau `AbstractHornetHttpMessageConverter` #####

- Créer une interface qui étend `ExportModelService`
    - déclarer une fonction permettant de créer le model à exporter. L'objet de sortie sera à déterminer en fonction de la librairie utilisée pour l'export (Jasper, POI, autre...)
- Créer une classe qui étend `AbstractHornetHttpMessageConverter`
    - la typer avec l'interface qui vient d'être crée
    - injecter par annotation les services de l'interface associée
    - appeler le constructeur parent avec le mime-type géré
    - implémenter la méthode écrireFichier : écrit le model dans le flux de sortie

__Exemple de code pour l'export XLS :__

- Interface :

```java
public interface ExportXlsModelService<T> extends ExportModelService<T> {

    public HSSFWorkbook construireXlsModel(T toExport);

}

```

- HttpMessageConverter :

```java
@Service
public class XlsHttpMessageConverter<T> extends
            AbstractHornetHttpMessageConverter<T, ExportXlsModelService<T>> {

    @Resource
    private ExportXlsModelService<T>[] exportServices;

    public XlsHttpMessageConverter() {

        super(HornetMediaType.APPLICATION_EXCEL);
    }

    @Override
    protected void ecrireFichier(final T toExport, final OutputStream os) throws IOException,
        HttpMessageNotWritableException {

        final ExportXlsModelService<T> exportService = getServiceExport(toExport);

        final HSSFWorkbook model = exportService.construireXlsModel(toExport);
        model.write(os);
    }

    @Override
    protected ExportXlsModelService<T>[] getServicesExport() {

        return exportServices;
    }

}
```

##### Déclaration du convertisseur auprès de Spring #####

Modifier la classe `WebConfig` :

- Injecter le convertisseur qui vient d'être créé (dans l'exemple `CustomHttpMessageConverter`)
- L'ajouter à la liste des convertisseurs
- Déclarer le nouveau mime-type géré et faire le mapping avec le paramètre d'url `mediaType`

Exemple de code :

```java
@Configuration
@EnableWebMvc
public class WebConfig extends HornetWebConfig {

    @Resource
    private CustomHttpMessageConverter<?> customConverter;

    @Override
    public void configureContentNegotiation(final ContentNegotiationConfigurer configurer) {

        super.configureContentNegotiation(configurer);

        // Ajouter ici les mimeTypes des contenus gérés par l'appli
`       configurer.mediaType("custom", "application/custom");
    }

    @Override
    public void configureMessageConverters(final List<HttpMessageConverter<?>> converters) {

        super.configureMessageConverters(converters);
        converters.add(customConverter);

    }

}
```

### Export de tableaux ###

Le composant tableau de l'application Javascript se base sur les données d'un objet Java `Table<T>` (exemple `Table<Partenaire>`).

Afin de mutualiser la mise en page des objets `Table<T>`, un service d'export abstrait a été mis en place dans le framework : `AbstractTableExportService`.

#### AbstractTableExportService ####

Ce service abstrait implémente :

- `ExportCsvModelService`
- `ExportXlsModelService`

En effet, ces deux formats de sorties partagent tous deux leur modèle de base : `TableVO`.
En revanche, le format PDF ne peut pas être géré de manière générique pour tous les tableaux, ce service abstrait n'implémente donc pas `ExportPdfModelService``.


#### Exemple d'utilisation ####

```java

@Service
public class PartenaireTableExportService extends AbstractTableExportService<TablePartenaire> {

    private static final Logger LOG = LoggerFactory.getLogger(PartenaireTableExportService.class);

    /**
     * Indique que ce service gère les Table de <Partenaire> : <TablePartenaire>
     */
    @Override
    public Class<TablePartenaire> getSupportedClass() {

        return TablePartenaire.class;
    }

    /**
     * Construit un vo table pour l'export a partir d'une liste de partenaires
     *
     * Utilisé par l'export XLS et CSV
     *
     * @param listePartenaires
     *            Liste de partenaires
     * @return TableVO contenant les donnees de la liste de partenaires
     */
    @Override
    protected TableVO construireTableauExport(final TablePartenaire tablePartenaires) {

        final List<Partenaire> listePartenaires = tablePartenaires.getListe();

        final TableVO table = new TableVO();
        table.setNbColumns(6);

        table.setDate(new Date());
        final List<RowVO> lignes = new ArrayList<RowVO>();
        table.setRows(lignes);
        // Titre des colonnes
        final List<String> titles = new ArrayList<String>();
        titles.add("Nom");
        // {...}

        table.setColumnsTitles(titles);

        if (listePartenaires != null) {
            final Iterator<Partenaire> it = listePartenaires.iterator();

            RowVO row;
            List<ColVO> cols;
            while (it.hasNext()) {
                final Partenaire partenaire = it.next();

                row = new RowVO();
                cols = new ArrayList<ColVO>();

                final ColVO colNom = new ColVO();
                colNom.setValue(partenaire.getNom());
                // {...}
                cols.add(colNom);
                // {...}

                row.setCols(cols);

                table.getRows().add(row);
            }
        }

        return table;
    }

```

Ce service `PartenaireTableExportService` appraitra à la fois dans la liste de services de `CsvHttpMessageConverter` et dans la liste de services de `XlsHttpMessageConverter`.

### Diagramme de classes ###

Récapitulatif de la structure présentée dans ce chapitre :

![Diagramme de classes](./sources/structure-exports.png)
