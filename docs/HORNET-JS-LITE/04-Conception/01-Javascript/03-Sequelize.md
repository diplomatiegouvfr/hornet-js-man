# Sequelize

`Sequelize` est un ORM développé en pure javascript. Il supporte plusieurs serveurs de base de données tel que `MySQL`, `Postgres`, `SQLite` and `MSSQL`.

Liens intéressants :

- https://github.com/sequelize/sequelize/wiki
- http://docs.sequelizejs.com/

## Dépendence Sequelize

### Hornet-js-database

Le framework hornet met à disposition un module nommé `hornet-js-database`. Ce module porte la dépendance à Sequelize. Il suffit donc d'ajouter ce module comme dépendance d'une application hornet pour utiliser une base données avec l'application.

```json
"appDependencies": {
    ...
    "hornet-js-database": "5.x.x"
  }
```

## Connecter une Base de données

### Gestion des dépendances

Pour fonctionner avec une application hornet, les serveurs de base de données, en plus de sequelize, ont besoin de certaines dépendances qui les concernent.

Pour fonctionner avec une base de données postgresql, il y a des dépendances à ajouter dans le `package.json`

```json
"appDependencies": {
    ...
    "pg": "7.3.0",
    "pg-hstore": "2.3.2",
    "pg-native": "2.2.0"
  }
```

Pour fonctionner avec une base de données SQLite :

```json
"appDependencies": {
    ...
    "sqlite3": "3.1.8"
  }
```

### Configuration

La configuration d'une base de données pour une application hornet est réalisée dans le fichier `config/default.json`

Un exemple d'un pool de database contenant une configuration postgresql et une configuration sqlite.

```json
"database": {
    "config": {
      "basename": "sequelize",
      "username": "test",
      "password": "test",
      "options": {
        "dialect": "sqlite",
        "storage": "./database/database.sqlite",
        "pool": {
          "max": 15,
          "min": 0,
          "idle": 1000
        },
        "define": {
          "timestamps": false
        },
        "loggingLevel": "DEBUG"
      },
      "reload": true
    },
    "configPostgres": {
      "uri": "postgres://hornetjs@localhost:5433/applitutorielDB",
      "options": {
        "pool": {
          "max": 5,
          "min": 0,
          "idle": 1000
        },
        "define": {
          "timestamps": false
        },
        "loggingLevel": "INFO"
      }
    }
}
```

## Utilisation de Sequelize

Afin d'utiliser correctement Sequelize dans l'application pour faire communiquer une application hornet avec une base de données relationnelle, il est nécessaire de définir les structures des données à l'image des tables côté base de données ainsi qu'à l'image des objets métiers de l'application.

En terme d'architecture, on obtient une application en différentes couches :

- Metier
- DTO (couche masquée par des décorateurs)
- DAO
- Structure Sequelize/Bdd

### Définition des structures de données

Au sein de l'application, on définit ce que l'on appelle les Sequelize models. Il s'agit de structures à l'image de celle présentent en base de données.

http://docs.sequelizejs.com/manual/tutorial/models-definition.html

Sequelize permet de définir pour chaque objet et chacun de ses attributs :

- le type de données
- le nom de la colonne liée, s'il s'agit d'une clé primaire ou d'une référence à une clé étrangère.

(Consulter la documentation Sequelize pour plus d'information)

```javascript
export var PartenaireModel:Sequelize.DefineAttributes = {
    id: {
        type: Sequelize.INTEGER,
        field: "id_partenaire",
        autoIncrement: true,
        primaryKey: true
    },
    ville: {
        type: Sequelize.INTEGER,
        field: "id_ville",
        allowNull: false,
        references: {
            model:"VilleModel",
            key:"idVille"
        }
    },
    civilite: {
        type: Sequelize.INTEGER,
        field: "id_civilite",
        allowNull: false,
        references: {
            model:"CiviliteModel",
            key:"idCivilite"
        }
    },
    nationalite: {
        type: Sequelize.INTEGER,
        field: "id_pays",
        allowNull: false,
        references: {
            model:"PaysModel",
            key:"idPays"
        }
    },
    photo: {
        type: Sequelize.INTEGER,
        field: "id_photo",
        references: {
            model:"PhotoModel",
            key:"idPhoto"
        }
    },
    isClient: {
        type: Sequelize.BOOLEAN,
        field: "par_is_client"
    },
    isVIP: {
        type: Sequelize.BOOLEAN,
        field: "par_is_vip"
    },
    nom: {
        type: Sequelize.STRING(50),
        field: "par_nom"
    },
    prenom: {
        type: Sequelize.STRING(50),
        field: "par_prenom"
    },
    nomLocal: {
        type: Sequelize.STRING(50),
        field: "par_nom_local"
    },
    prenomLocal: {
        type: Sequelize.STRING(50),
        field: "par_prenom_local"
    },
    dateNaissance: {
        type: Sequelize.DATE,
        field: "par_date_naissance",
    },
    organisme: {
        type: Sequelize.STRING(50),
        field: "par_organisme"
    },
    fonction: {
        type: Sequelize.STRING(50),
        field: "par_fonction"
    },
    proTelFixe: {
        type: Sequelize.STRING(16),
        field: "par_pro_tel_fixe"
    },
    proTelPort: {
        type: Sequelize.STRING(16),
        field: "par_pro_tel_port"
    },
    proCourriel: {
        type: Sequelize.STRING(80),
        field: "par_pro_courriel"
    },
    proFax: {
        type: Sequelize.STRING(16),
        field: "par_pro_fax"
    },
    proAdrRue: {
        type: Sequelize.STRING(250),
        field: "par_pro_adr_rue"
    },
    proAdrCP: {
        type: Sequelize.STRING(9),
        field: "par_pro_adr_cp"
    },
    assistNom: {
        type: Sequelize.STRING(50),
        field: "par_assist_nom"
    },
    assistPrenom: {
        type: Sequelize.STRING(50),
        field: "par_assist_prenom"
    },
    assistTel: {
        type: Sequelize.STRING(16),
        field: "par_assist_tel"
    },
    assistCourriel: {
        type: Sequelize.STRING(80),
        field: "par_assist_courriel"
    },
    commentaire: {
        type: Sequelize.STRING(500),
        field: "par_commentaire"
    },
    satisfaction: {
        type: Sequelize.STRING(500),
        field: "satisfaction"
    },
    dateCreat: {
        type: Sequelize.DATE,
        field: "par_date_creation"
    },
    dateMaj: {
        type: Sequelize.DATEONLY,
        field: "par_date_modification"
    }
};
```

#### Colonne de type Enum

Sequelize prend en compte aussi le type ENUM. Ci-dessous, un cas d'utilisation:

```javascript
export let CiviliteModel: Sequelize.DefineAttributes = {
    id: {
        type: Sequelize.INTEGER,
        field: "id_civilite",
        primaryKey: true,
        allowNull: false,
        unique: "civilite_pkey"
    },
    libelle: {
        type: Sequelize.ENUM,
        field: "civ_libelle",
        allowNull: false,
        defaultValue: "Mme",
        values: ['Mr', 'Mme']
    }
};
```
Le modèle `CiviliteModel` déclare deux attributs:
 - id : représente la clé primaire de la table associée et est de type Sequelize.INTEGER
 - libelle : représente le libellé de la civilité et est de type Sequelize.ENUM. Les valeurs possibles sont données par le tableau `values`

Avec cette définition, les tentatives d'insertion de données(en passant pas sequelize) dans la table associée au modèle `CiviliteModel`, se veront rejetées pour cause d'invalidité si la valeur de l'attribut `libelle` ne se trouve pas dans le tableau `values`.

Si en base de données, la colonne est déclarée comme étant de type enum mais est déclarée autrement avec Sequelize, l'erreur `SequelizeUniqueConstraintError` est quand-même remontée si la contrainte définie en base n'est pas respectée lors de l'insertion/maj de la colonne.

Pour plus de sécurité, il faudra aussi définir cette contrainte lors de la déclaration de la table.

Ci-dessous, les définitions pour Sqlite et Postgresql.

##### Déclaration type Enum avec Sqlite

Au moment de la rédaction de cette documentation, le type Enum n'est pas supporté par Sqlite. Il faudra ajouter un contrôle supplémentaire pour limiter les valeurs possibles d'une colonne.
```sql
CREATE TABLE CIVILITE (
    ID_CIVILITE INTEGER PRIMARY KEY AUTOINCREMENT
    , CIV_LIBELLE VARCHAR(3) CHECK(CIV_LIBELLE IN ('Mme', 'Mr')));
```
L'instruction `CHECK` restreint les valeurs possibles de la colonne `CIV_LIBELLE` à `Mme` et `Mr`

##### Déclaration type Enum avev Postgresql

Avec Posgresql, il faudra créer un nouveau type et préciser ensuite que la colonne `CIV_LIBELLE` est de ce type.
```sql
CREATE TYPE CIV AS ENUM ('Mme', 'Mr');
CREATE TABLE CIVILITE (
    ID_CIVILITE SERIAL PRIMARY KEY
    , CIV_LIBELLE CIV DEFAULT 'Mme');
```
Ce référer à la documentation pour plus de détails.

### Définition des structures métiers

Les applications Hornet utilisent une couche métier dans laquelle on définit la structure des objets métiers à utiliser.

Des decorators en provenance du projet `hornet-js-bean` sont à utiliser :

- Bean
- Map
- Alias

(Consulter la documentation `hornet-js-bean` pour plus d'information)

```javascript
@Bean
export class PartenaireMetier {
    @Map()
    id: number;

    @Map(VilleMetier)
    @Alias("laVille")
    ville: VilleMetier;

    @Map(CiviliteMetier)
    @Alias("laCivilite")
    civilite: CiviliteMetier;

    @Map(PaysMetier)
    @Alias("laNationalite")
    nationalite: PaysMetier;

    @Map(PhotoMetier)
    @Alias("laPhoto")
    photo: PhotoMetier;

    @Map(ProduitMetier)
    @Alias("listeProduit")
    listeProduit: Array<ProduitMetier>;

    @Map()
    isClient: boolean;

    @Map()
    isVIP: boolean;

    @Map()
    nom: string;

    @Map()
    prenom: string;

    @Map()
    nomLocal: string;

    @Map()
    prenomLocal: string;

    @Map()
    dateNaissance: Date;

    @Map()
    organisme: string;

    @Map()
    fonction: string;

    @Map()
    proTelFixe: string;

    @Map()
    proTelPort: string;

    @Map()
    proCourriel: string;

    @Map()
    proFax: string;

    @Map()
    proAdrRue: string;

    @Map()
    proAdrCP: string;

    @Map()
    assistNom: string;

    @Map()
    assistPrenom: string;

    @Map()
    assistTel: string;

    @Map()
    assistCourriel: string;

    @Map()
    commentaire: string;

    @Map()
    satisfaction: Satisfaction | string;

    @Map()
    dateCreat: Date;

    @Map()
    dateMaj: Date;

    @Map()
    proAutresTels: String[];
}
```

### Utilisation d'HornetSequelizeModel

`HornetSequelizeModel` est un objet du framework servent à définir les entities en fonction des structures définies dans les Sequelize models.

Ces entities serviront dans la couche DAO pour les requêtes à destination de la base de données.

Chaque implémentation d'`HornetSequelizeModel` est liée à une configuration d'une base de données, à partir de leur nom comme défini dans le `default.json`.

Pour chaque configuration d'une base de données on y répertorie les entities et les relations entre elles.

On y retrouve les entités d'une base de données et leurs relations entre elles.

Grâce au décorateur @Entity, pour chaque entity, on indique :

- le nom de la table liée
- la référence au sequelize model correspondant

ex :
```javascript
    @Entity("partenaire", PartenaireModel)
    public partenaireEntity: HornetSequelizeInstanceModel<PartenaireAttributes>;
```

Ces entities sont typées en `HornetSequelizeInstanceModel<T>` qui surcharge `Sequelize.Model<TInstance, TAttributes>`, les relations entre elles sont à définir au travers de Sequelize et/ou de `SequelizeUtils`.

Dans l'exemple ci-dessous, l'entity Partenaire est définie avec 5 relations à d'autre entitées, donc un exemple de relation one-to-many.

```javascript
    private initPartenaireEntity(): void {
        SequelizeUtils.initRelationBelongsTo({fromEntity: this.partenaireEntity, toEntity: this.paysEntity, alias: "laNationalite", foreignKey: "id_pays"});
        SequelizeUtils.initRelationBelongsTo({fromEntity: this.partenaireEntity, toEntity: this.villeEntity, alias: "laVille", foreignKey: "id_ville"});
        SequelizeUtils.initRelationBelongsTo({fromEntity: this.partenaireEntity, toEntity: this.civiliteEntity, alias: "laCivilite", foreignKey: "id_civilite"});
        SequelizeUtils.initRelationBelongsToMany({fromEntity: this.partenaireEntity, toEntity: this.produitEntity, alias: "listeProduit", foreignKey: "id_partenaire", throughTable: "produit_partenaire"});
        SequelizeUtils.initRelationBelongsTo({fromEntity: this.partenaireEntity, toEntity: this.photoEntity, alias: "laPhoto", foreignKey: "id_photo"});
    }
```

Les relations sont initialisées depuis le constructeur :

```javascript
    constructor(@inject("configApplitutoDatabase")conf?: string) {
        super(conf);
        this.initVilleEntity();
        this.initUtilisateurEntity();
        this.initRoleEntity();
        this.initPartenaireEntity();
        this.initProduitEntity();
    }
```

Il est conseillé de rentre les classes héritant d'`HornetSequelizeModel` injectables grâce au décorateur `injectable` et d'injecter le nom de la configuration de la base de données dans le constructor grâce au décorateur `inject`.

```javascript
    @injectable(ModelDAO, Scope.SINGLETON, Side.SERVER)
    export class ModelDAO extends HornetSequelizeModel {
        ...
    }
```

Dans l'exemple ci-dessous, "configApplitutoDatabase" est une clée afin de récupérer une valeur dans l'`Injector` d'`hornet-js-core`

```javascript
    constructor(@inject("configApplitutoDatabase")conf?: string) {
        super(conf);
        ...
    }
```

Exemple de la classe ModelDAO de l'application Tutorial :

```javascript
@injectable(ModelDAO, Scope.SINGLETON, Side.SERVER)
export class ModelDAO extends HornetSequelizeModel {

    @Entity("ville", VilleModel)
    public villeEntity: HornetSequelizeInstanceModel<VilleAttributes>;

    @Entity("pays", PaysModel)
    public paysEntity: HornetSequelizeInstanceModel<PaysAttributes>;

    @Entity("civilite", CiviliteModel)
    public civiliteEntity: HornetSequelizeInstanceModel<CiviliteAttributes>;

    @Entity("produit", ProduitModel)
    public produitEntity: HornetSequelizeInstanceModel<ProduitAttributes>;

    @Entity("secteur", SecteurModel)
    public secteurEntity: HornetSequelizeInstanceModel<SecteurAttributes>;

    @Entity("utilisateur", UtilisateurModel)
    public utilisateurEntity: HornetSequelizeInstanceModel<UtilisateurAttributes>;

    @Entity("role", RoleModel)
    public roleEntity: HornetSequelizeInstanceModel<RoleAttributes>;

    @Entity("role_utilisateur", RoleUtilisateurModel)
    public roleUtilisateurEntity: HornetSequelizeInstanceModel<RoleUtilisateurAttributes>;

    @Entity("partenaire", PartenaireModel)
    public partenaireEntity: HornetSequelizeInstanceModel<PartenaireAttributes>;

    @Entity("produit_partenaire", ProduitPartenaireModel)
    public produitPartenaireEntity: HornetSequelizeInstanceModel<ProduitPartenaireAttributes>;

    @Entity("photo", PhotoModel)
    public photoEntity: HornetSequelizeInstanceModel<PhotoAttributes>;

    constructor(@inject("configApplitutoDatabase")conf?: string) {
        super(conf);
        this.initVilleEntity();
        this.initUtilisateurEntity();
        this.initRoleEntity();
        this.initPartenaireEntity();
        this.initProduitEntity();
    }

    /** METHODS */

    private initVilleEntity(): void {
        SequelizeUtils.initRelationBelongsTo({fromEntity: this.villeEntity, toEntity: this.paysEntity, alias: "lePays", foreignKey: "id_pays"});
    }

    private initUtilisateurEntity(): void {
        SequelizeUtils.initRelationBelongsToMany({fromEntity: this.utilisateurEntity, toEntity: this.roleEntity, alias: "listeRole", foreignKey: "id_utilisateur", throughTable: "role_utilisateur"});
    }

    private initRoleEntity(): void {
        SequelizeUtils.initRelationBelongsToMany({fromEntity: this.roleEntity, toEntity: this.utilisateurEntity, alias: "listeUser", foreignKey: "id_role", throughTable: "role_utilisateur"});
    }

    private initPartenaireEntity(): void {
        SequelizeUtils.initRelationBelongsTo({fromEntity: this.partenaireEntity, toEntity: this.paysEntity, alias: "laNationalite", foreignKey: "id_pays"});
        SequelizeUtils.initRelationBelongsTo({fromEntity: this.partenaireEntity, toEntity: this.villeEntity, alias: "laVille", foreignKey: "id_ville"});
        SequelizeUtils.initRelationBelongsTo({fromEntity: this.partenaireEntity, toEntity: this.civiliteEntity, alias: "laCivilite", foreignKey: "id_civilite"});
        SequelizeUtils.initRelationBelongsToMany({fromEntity: this.partenaireEntity, toEntity: this.produitEntity, alias: "listeProduit", foreignKey: "id_partenaire", throughTable: "produit_partenaire"});
        SequelizeUtils.initRelationBelongsTo({fromEntity: this.partenaireEntity, toEntity: this.photoEntity, alias: "laPhoto", foreignKey: "id_photo"});
    }

    private initProduitEntity(): void {
        SequelizeUtils.initRelationBelongsToMany({fromEntity: this.produitEntity, toEntity: this.partenaireEntity, alias: "partenaires", foreignKey: "id_produit", throughTable: "produit_partenaire"});
    }
}
```

Pour l'option throughTable il est également possible de passer une entité:

```javascript
SequelizeUtils.initRelationBelongsToMany({fromEntity: this.partenaireEntity, toEntity: this.produitEntity, alias: "listeProduit", foreignKey: "id_partenaire", throughTable: this.produitPartenaireEntity });
```

### Connecteur à la base de données

le connecteur de base de données `DbConnect` est un objet du projet `hornet-js-database` permettant d'ouvrir la connexion à la base de données définie.

Il sera utilisé par les DAO lors de chaque requête vers la base de données.

Son utilisation est fournie grâce à l'object `HornetSequelizeEntity`

Dans l'exemple ci-dessous, on réalise une connexion avec la base de données liée à `ModelDAO`. Notez l'utilisation des décorateurs `injectable` et `inject`

```javascript
@injectable()
export class EntityDAO extends HornetSequelizeEntity<ModelDAO> {

    constructor(@inject(ModelDAO) modelDAO?: ModelDAO) {
        super(modelDAO);
    }
}
```

Depuis la version 5.2.0 il n'est plus nécessaire de créer une classe héritant de HornetSequelizeEntity comme peut le faire la classe EntityDAO. Cette façon de faire devient maintenant @deprecated.

Tout est pris en compte dans l'instanciation des DAO (voir plus bas)

### La couche DAO

Chacun des DAO hérite du connecteur de base de données mentionné ci-dessus.

Chaque DAO est lié à une entity. On y trouve toutes les méthodes réalisant des requêtes vers la table liée à l'entity.

```javascript
export class PartenaireDAO extends EntityDAO {
    constructor() {
        super();
    }

    @Map(PartenaireMetier)
    selectByCriteres(isClient: boolean, isVIP: boolean, startDate, sortColumn: string) : Promise<Array<PartenaireMetier>>{
        return this.modelDAO.partenaireEntity.findAll({
            where: {
                isClient: '' + isClient,
                isVIP: ''+isVIP,
                dateMaj: {
                    $gte: startDate
                }
            },
           include : [{
                model: this.modelDAO.villeEntity,
                as :"laVille",
            }],
            order: [[sortColumn, "ASC"]]
        })
    }
}
```

(Consulter la documentation `[Sequelize](http://docs.sequelizejs.com/manual/tutorial/querying.html)` pour plus d'information)

A Savoir :
Sequelize retourne des instances à l'image des structures définies en `Sequelize.DefineAttributes`.

Le décorateur `@Map` permetant de transformer cette instance sequelize en objet métier. (consultez la documentation `hornet-js-bean` pour plus d'information).

Important :
Depuis la version 5.2.0 il n'est plus nécessaire de réaliser un héritage avec la classe EntityDAO. Le framework met à disposition l'objet :
HornetGenericDAO qui se charge de piloter la connexion à la base de données si vos DAO héritent de cet objet.

Cet objet HornetGenericDAO a également l'avantage d'implémenter les principaux types de requêtes afin de ne pas les recréer dans chacun des DAO d'un projet. Ces méthodes peuvent être dirèctement appelées depuis vos services.

```javascript
import { HornetGenericDAO } from "hornet-js-database/src/sequelize/hornet-generic-dao";

@injectable()
export class PartenaireDAO extends HornetGenericDAO<ModelDAO, HornetSequelizeInstanceModel<PartenaireAttributes>> {
    constructor(entity: string = "partenaireEntity", @inject(ModelDAO) modelDAO?: ModelDAO) {
        super(modelDAO[entity], modelDAO);
    }

    @Map(PartenaireMetier)
    selectByCriteres(client: boolean, vip: boolean, startDate, sort: {}): Promise<PartenaireMetier[]> {
        let criteria = {
            where: {
                client: client,
                dateModif: {
                    [ Op.gte ]: startDate
                },
            },
            include: [ {
                model: this.modelDAO.villeEntity,
                as: "laVille",
            } ],
            order: [ seqTri ],
        };
        if (vip !== undefined) {
            criteria.where["vip"] = vip;
        }
        return this.entity.findAll(criteria);
    }
}
```

Instanciation dans un service :

```javascript
private partenaireDAO: PartenaireDAO = new PartenaireDAO();
```

### Couche Service et Transaction

Sequelize supportant les transactions. Le projet `hornet-js-database` apporte un décorateur permettant d'ouvrir une transaction afin de ne commiter le résultat des requêtes SQL qu'une fois le service correctement terminé.

```javascript
    @Transactional({configDatabase: Injector.getRegistered("configApplitutoDatabase")})
        modifier(id: any, partenaire: any): Promise<any> {
            logger.info("modifier id :", id);
             return this.insertPhoto(partenaire).then((retourPartenaire) => {
                if (id != null) {
                    this.partenaireDAO.updateById(id, retourPartenaire).then(retourApi => {
                        logger.info("Retour d\"enregistrement OK", retourApi);
                        return retourApi;
                    });
                } else {
                    this.partenaireDAO.insert(retourPartenaire).then(retourApi => {
                        logger.info("Retour d\"enregistrement OK", retourApi);
                        return retourApi;
                    });
                }
            });
        }
```
