# Partie Données (Intégration)

## Architecture

Le framework MyBatis est composé de deux modules : MyBatis (core) et MyBatis–Spring.

MyBatis–Spring est utilisé afin notamment de bénéficier de la gestion des transactions au sein des services gérés par Spring.

## Création d'un DAO

### Création de la classe DAO

- Créer une classe qui étend _org.mybatis.spring.support.SqlSessionDaoSupport.SqlSessionDaoSupport_
- Ajouter les méthodes de CRUD (Create / Read / Update / Delete)
- Les accès en base se font via la session fournie par Spring au parent :  

```java
    this.getSqlSession()...
```

- Les requêtes ne sont pas écrites directement dans le DAO mais dans des fichiers de mapping XML. Le DAO accède à ces requêtes via des clés.  

Exemple : `partenaire.selectById`
  
La première partie de la clé (`partenaire`) fait référence au namespace du fichier de mapping.  
La seconde partie de la clé (`selectById`) fait référence à l'id de la requête SQL dans le fichier de maping.

__Exemple de DAO :__

```java
    public class PartenaireDAO extends SqlSessionDaoSupport {

        /**
         * retrieveDataList
         *
         * @return List < Partenaire >
         */
        public List<Partenaire> retrieveDataList() {

            return this.getSqlSession().selectList("partenaire.select");
        }

        /**
         * @param id
         *            Long
         * @return Partenaire partenaire
         */
        public Partenaire selectById(final Long id) {

            final Map<String, String> params = new HashMap<String, String>();
            params.put("id", id.toString());
            return (Partenaire) this.getSqlSession().selectOne("partenaire.selectById", params);
        }

        /**
         * @param partenaire
         *            Partenaire
         */
        public void deletePartenaire(final Partenaire partenaire) {

            this.getSqlSession().delete("partenaire.delete", partenaire);
        }
    }
```


Ce fichier fait appel à trois requêtes de l'espace de nom `partenaire` : `select`, `selectById`, `delete`.

La déclaration de ces requêtes est présentée ci-dessous.


### Création du fichier de mapping

 - Créer un fichier `[Nom du DAO]_SqlMap.xml` dans les ressources, dans le même package que le DAO précédemment créé. (Exemple : `Partenaire_SqlMap.xml`).
 - Déclarer le `namespace` du `mapper` (Exemple : `partenaire`)
 - Déclarer les mappings entre les colonnes de la table et un BO
 - Déclarer et écrire les différentes requêtes

L'écriture des requêtes est détaillée plus loin dans ce document (paragraphe Développement spécifique MyBatis)

__Exemple de fichier :__

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE mapper
      PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
      "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

    <mapper namespace="partenaire">

        <resultMap type="fr.gouv.diplomatie.applitutoriel.business.bo.Partenaire" id="mapPartenaire">
            <result column="ID_PARTENAIRE" property="id" />
            <result column="PAR_NOM" property="nom" />
            <result column="PAR_PRENOM" property="prenom" />
        </resultMap>

        <sql id="selectPartenaire_fragment">
            SELECT
            ID_PARTENAIRE,
            PAR_NOM,
            PAR_PRENOM
        </sql>

        <select id="select" resultMap="mapPartenaire">
            <include refid="selectPartenaire_fragment" />
            ORDER BY PAR_NOM ASC;
        </select>

        <select id="selectById" resultMap="mapPartenaire">
            <include refid="selectPartenaire_fragment" />
            WHERE ID_PARTENAIRE ={id};
        </select>

        <delete id="delete" parameterType="fr.gouv.diplomatie.applitutoriel.business.bo.Partenaire">
            DELETE FROM PARTENAIRE WHERE ID_PARTENAIRE ={id}
        </delete>
    </mapper>
```

On retrouve dans cet exemple les trois requêtes de l'exemple sur la création d'un DAO : `partenaire.select`, `partenaire.selectById`, `partenaire.delete`.

Les requêtes de select sont mappées sur le BO `Partenaire`.

## Intégration dans Spring

### Déclaration de la dataSource

Non spécifique MyBatis.

- Créer un fichier `spring-appContext-common-datasource.xml` dans le dossier des ressources et ayant cette structure :  

```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:jee="http://www.springframework.org/schema/jee" xmlns:tx="http://www.springframework.org/schema/tx" xmlns:jdbc="http://www.springframework.org/schema/jdbc"
        xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.1.xsd
        http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.1.xsd
        http://www.springframework.org/schema/jee http://www.springframework.org/schema/jee/spring-jee-4.1.xsd
        http://www.springframework.org/schema/jdbc http://www.springframework.org/schema/jdbc/spring-jdbc-4.1.xsd">

        <!-- Initialisation de la datasource -->
        <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
        [...]
        </bean>
    </beans>
```

- Paramétrer la `dataSource` avec les informations de connexions à la base de données.  
  Il est recommandé d'aller lire ces informations dans un fichier de propriétés.

### Création du fichier de configuration MyBatis

- Créer un fichier `mybatis-config.xml` dans le dossier des ressources et ayant cette structure :  

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE configuration
      PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
      "http://mybatis.org/dtd/mybatis-3-config.dtd">
    <configuration>
        <settings>
            <setting name="logImpl" value="SLF4J" />
        </settings>
        <mappers>
            <mapper resource="fr/gouv/diplomatie/applitutoriel/integration/dao/maps/Exemple_SqlMap.xml" />
        </mappers>
    </configuration>
```


### Déclaration des objets spécifiques MyBatis

- Créer un fichier `spring-appContext-common-dao.xml` dans le dossier des ressources et ayant cette structure :  

```xml

	<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:cache="http://www.springframework.org/schema/cache" xmlns:mybatis="http://mybatis.org/schema/mybatis-spring"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.1.xsd
						http://www.springframework.org/schema/cache http://www.springframework.org/schema/cache/spring-cache-4.1.xsd
						http://mybatis.org/schema/mybatis-spring http://mybatis.org/schema/mybatis-spring.xsd">

        <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
            <property name="dataSource" ref="dataSource" />
            <property name="configLocation" value="classpath:mybatis-config.xml"/>
        </bean>
        
        <!-- #### DEBUT Gestion du cache #### -->
		 <cache:annotation-driven cache-manager="cacheManager" />
		 <bean id="cacheManager" class="org.springframework.cache.ehcache.EhCacheCacheManager">
    		<property name="cacheManager" ref="ehCacheManager"/>
		 </bean>

		 <bean id="ehCacheManager" class="org.springframework.cache.ehcache.EhCacheManagerFactoryBean">
			<property name="configLocation" value="classpath:ehcache.xml" />
			<property name="shared" value="true" />
		</bean>

        <!-- Liste des DAOs -->
        <bean id="exempleDAO" class="fr.gouv.diplomatie.applitutoriel.integration.dao.ExempleDAO">
            <property name="sqlSessionFactory" ref="sqlSessionFactory" />
        </bean>

    </beans>
```

- Le bean `sqlSessionFactory` permet l'intégration des DAOs avec Spring. Ce bean sera injecté à chacun des DAOs (voir bean `exempleDAO`). Il leur fournira une session pour exécuter les requêtes.

### Import des fichiers Spring

- Ajouter l'import des fichiers de conf Spring créés précédemment dans le fichier de conf Spring parent.

```xml
    <import resource="classpath:/spring-appContext-common-datasource.xml"/>
    <import resource="classpath:/spring-appContext-common-dao.xml"/>
```

## Développement spécifique MyBatis

Les fichiers de mapping XML MyBatis doivent être écrits en suivant les règles suivantes :

- Écrire du SQL standard tant que c’est possible (i.e. : éviter les appels de fonction ou procédures stockées spécifiques au SGBD), afin d’améliorer la portabilité et ainsi permettre l’utilisation de HSQLDB pour les tests unitaires.
- Les requêtes avec jointure doivent utiliser la syntaxe `(LEFT OUTER) JOIN … ON` et non la forme `FROM A,B`
- On pourra mapper les jointures sur un graphe d’objet BO uniquement si l’isolation par les VO n’est pas nécessaire (gain de temps).
- Si une requête utilise du SQL spécifique SGBD, ajouter à son id un suffixe correspondant au nom du sgbd :  

```xml
    <insert id="insertProduct-ORACLE" parameterClass="com.domain.Product">
        ...
    </insert>
```

### Ajout d'une requête

#### Select avec jointure

__Règle__ : Si une requête de sélection comporte des jointures, elle doit figurer dans le fichier `SqlMap.xml` correspondant à la table principale de la requête.

__Règle__ : Les données renvoyées par une requête de jointure peuvent être mappées sur un VO spécifique à cette jointure, ou directement un BO.

On pourra soit utiliser les alias « as » SQL pour le mapping des colonnes sur les propriétés de la classe ou définir un `ResultMap`. L’intérêt du `ResultMap` est d’être réutilisable et pouvoir gérer les graphes d’objets.

Exemple de mapping sur un VO spécifique :

- Ajouter dans le fichier `person_SqlMap.xml` :  

```xml
    <select id="getPersonWithRole" parameterType=”int” resultType="hornet.projet.integration.vo.PersonWithRole">
    SELECT 	PER_ID as id,
        PER_FIRST_NAME as firstName,
        PER_LAST_NAME as lastName,
        PER_BIRTH_DATE as birthDate,
        PER_WEIGHT_KG as weightInKilograms,
        PER_HEIGHT_M as heightInMeters,
        ROL_NAME as roleName
    FROM PERSON
    JOIN ROLE on PERSON.ROL_ID = ROLE.ROL_ID
    WHERE PER_ID ={value}
    </select>
```
- Créer une classe java `hornet.projet.integration.vo.PersonWithRole` avec les propriétés nécessaires.

#### Insert / Update / Delete

__Règle__ : Les données à mettre à jour doivent être modélisées par une classe Java, dont le nom est suffisamment explicite. La mise à jour portant sur une table à la fois, on pourra réutiliser la classe VO appropriée.

Chaque paramètre de requête `#{param}` correspond au nom d’une propriété Javabean.

__Exemple :__  

```xml
    <insert id="insertPerson" parameterType="projet.integration.vo.Person">
    INSERT INTO
        PERSON (PER_ID, PER_FIRST_NAME, PER_LAST_NAME,
            PER_BIRTH_DATE, PER_WEIGHT_KG, PER_HEIGHT_M)
        VALUES (#{id},{firstName},{lastName},
           {birthDate},#{weightInKilograms},{heightInMeters})
    </insert>

    <update id="updatePerson" parameterType="projet.integration.vo.Person">
    UPDATE PERSON
        SET PER_FIRST_NAME ={firstName},
        PER_LAST_NAME ={lastName}, PER_BIRTH_DATE ={birthDate},
        PER_WEIGHT_KG ={weightInKilograms},
        PER_HEIGHT_M ={heightInMeters}
    WHERE PER_ID ={id}
    </update>

    <delete id="deletePersonById" parameterType="projet.integration.vo.Person">
    DELETE PERSON
    WHERE PER_ID ={id}
    </delete>
```

#### Récupération de la PK après insertion

La clé primaire peut être récupérée suite à une insertion grâce à l'attribut MyBatis `useGeneratedKeys` à `true`.

On indiquera également la colonne associée (`keyColumn`) ainsi que l'attribut associé dans le VO de retour (`keyProperty`).

```xml
    <insert id="insert" parameterType="projet.integration.vo.Person"
        useGeneratedKeys="true" keyProperty="id" keyColumn="PER_ID" >
        ...
    </insert>
```

#### Construction dynamique de requête SQL

Une requête dynamique peut être réalisée grâce au tag `<if>`, la condition sera écrite dans l'attribut `test`.

__Exemple :__

```xml
    <select id="selectByCriteres" resultMap="mapPartenaireCriteres">
		SELECT
		DISTINCT PARTENAIRE.ID_PARTENAIRE, PAR_IS_CLIENT, PAR_IS_VIP, PAR_NOM, PAR_PRENOM,
		PAR_PRO_COURRIEL, PAR_ORGANISME, PAR_FONCTION, PAR_DATE_CREATION, PAR_DATE_MODIFICATION
		FROM PARTENAIRE
		<if test="idSecteur != null">
			<if test="idSecteur != 0">
				INNER JOIN PRODUIT_PARTENAIRE ON PRODUIT_PARTENAIRE.ID_PARTENAIRE = PARTENAIRE.ID_PARTENAIRE
				INNER JOIN PRODUIT ON PRODUIT.ID_PRODUIT = PRODUIT_PARTENAIRE.ID_PRODUIT AND PRODUIT.ID_SECTEUR ={idSecteur}
			</if>
		</if>
		<where>
			<if test="partenaire.isClient != null and partenaire.isClient != ''" >
				AND PAR_IS_CLIENT ={partenaire.isClient}
			</if>
			<if test="partenaire.isVIP" >
				AND PAR_IS_VIP = 1
			</if>
			<if test="startDate != null and startDate != ''" >
				AND DATEDIFF('dd', PAR_DATE_MODIFICATION,{startDate}) &lt;= 0
			</if>
		</where>
	</select>
```

### N+1 selects

Le Problème des N+1 selects apparaît lorsqu’on souhaite charger un graphe d’objet. Par exemple quand on charge une liste de N objets avec 1 requête puis pour chaque objet, on effectue une nouvelle requête pour charger un objet lié. Pour résoudre ce problème on utilisera une jointure et un mapping approprié (`ResultMap`).

On pourra directement réutiliser le graphe d’objet des BO.

- Relation 1-1  
Le but est de Mapper sur un objet contenant une référence à un autre objet.
- Relation 1-N ou M-N  
Le but est de Mapper sur un objet contenant une collection, on utilise pour cela un ResultMap.

__Exemple de relation :__

```xml
	<resultMap type="fr.gouv.diplomatie.applitutoriel.business.bo.Ville" id="mapVille">
		<result column="ID_VILLE" property="id" />
		<result column="VIL_LIBELLE" property="libelle" />
		<association column="ID_PAYS" property="pays" resultMap="pays.mapPays" />
	</resultMap>

	<select id="select" resultMap="mapVille">
		SELECT ID_VILLE, VIL_LIBELLE
		, PAYS.ID_PAYS AS ID_PAYS, PAY_LIBELLE, PAY_NATIONALITE
		FROM VILLE
		JOIN PAYS ON VILLE.ID_PAYS = PAYS.ID_PAYS
	</select>
```

### Gestion des batchs updates

#### Principe

Les batchs updates sont des batchs JDBC. Le principe de ces batchs est d’éviter d’exécuter unitairement des ordres SQL de création, modification et suppression, surtout s’ils sont nombreux, afin d’optimiser les temps d’exécution.

Pour ce faire on distingue les ordres de création, de modification et de suppression que l’on stocke dans trois listes différentes.

Puis, on exécute dans un batch JDBC, par groupe, les ordres de création ensuite ceux de modification et enfin ceux de suppression.

Ainsi on réussit à agréger les ordres SQL et à les exécuter par paquets et non unitairement ce qui permet de multiplier par dix le gain d’exécution.

On utilise pour cela la balise `<foreach>`.

#### Exemple

```xml
    <foreach collection="attendingUsrList" item="model"  separator=";">
        UPDATE parties SET attending_user_count ={model.attending_count}
        WHERE  fb_party_id ={model.eid}
    </foreach>
```

## Cache de requêtes

### Principe

MyBatis permet de charger en mémoire des requêtes dans un cache afin d’éviter de relancer celles-ci si ce n’est pas nécessaire.

On peut ainsi diminuer le temps d’exécution et optimiser l’accès aux données.

Il est préférable d’utiliser ce cache en lecture seule, uniquement sur les données qui sont majoritairement accédées en lecture et dont la fréquence de mise à jour est faible.

Il faut impérativement définir la taille du cache et sa durée de vie pour renouveler les données stockées en mémoires.

En effet, il est important de déterminer quelles sont les requêtes à conserver dans le cache pour estimer la taille de celui-ci.

Il faut noter qu’une requête qui comporte plusieurs clauses dynamiques différentes engendre autant de requêtes à stocker en cache qu’il existe de combinaison possible pour ces clauses.

Ainsi, s’il existe au maximum deux clauses sur la requête, le cache contiendra quatre requêtes, une pour chaque combinaison possible.

Pour optimiser la mémoire du cache plusieurs astuces sont possibles :

- Appliquer des algorithmes supplémentaires pour améliorer la gestion du cache, tel que l’algorithme LRU qui supprime du cache les requêtes les moins utilisées.
- Modifier le code des requêtes de type « select » afin que celles-ci ne retournent que les identifiants. Puis, ajouter un algorithme qui parcourt ce résultat et qui effectue une requête sur les clefs primaires. Ce principe est un peu plus couteux à la première exécution mais il permet de stocker moins de requêtes dans le cache et de stocker tout l’objet.

Enfin, l’utilisation d’un cache n’a pas d’intérêt si celui-ci n’est pas utilisé à plus de 50%.

Exemple d’utilisation :

```xml
    <cache
      eviction="LRU"
      flushInterval="60000"
      size="512"
      readOnly="true"/>
```

## Limiter le nombre de résultats

- Cas Oracle :
    - Les 100 premiers résultats d'une requête non triée :  

```sql
    SELECT *
    FROM `t_client`
    WHERE ROWNUM <= 100
```

   - Les 100 premiers résultats d’une requête triée :  

```sql
    SELECT *
    FROM (SELECT * FROM `t_client` ORDER BY name)
    WHERE ROWNUM <= 100
```

Le compteur `ROWNUM`, initialisé à 1, est définit pour chaque résultat d’une requête. La valeur de celui-ci est attribuée puis incrémenté de 1 à chaque ligne retournée par la requête. Lorsque les données retournées ne sont pas triées avec le mot-clé `ORDER BY` ce principe ne pose pas de problème. Par contre, si on souhaite trier les données et tenir compte de l’ordre alors il est nécessaire d’utiliser une sous-requête. En effet, la valeur de `ROWNUM` est attribuée pour chaque ligne avant le tri final. Ainsi, une fois le résultat ordonné, le critère des 100 premiers ne peut être respecté.


- Cas MS SQL Serveur 2005 :
    - Les 100 premiers résultats :

```sql
SELECT TOP 100 *
FROM t_client
```

- Cas mySQL et postgreSQL :
    - Les 100 premiers résultats :

```sql
SELECT *
FROM `ARCHIVA_ARTIFACT`
LIMIT 100
```

## Pagination de listes

Plusieurs solutions sont possibles pour la pagination. Le choix se fait en fonction des volumes des données et fréquences d’utilisation et de mise à jour.

Les trois variantes les plus communes sont présentées ici.

### Paginer sur le sgbd

#### Cas Oracle

Il est possible d’utiliser la fonction analytique `ROW_NUMBER`, pour filtrer le résultat d’une requête sur des intervalles consécutifs.  
Le principe est assez similaire à `ROWNUM`. En effet, la fonction `ROW_NUMBER` est appliquée sur le résultat final de la requête alors que `ROWNUM` est appliqué pendant l’exécution de celle-ci.  
Exemple qui affiche 30 lignes à partir de l'enregistrement 10 :

   - avec ROWNUM :  

```sql
SELECT …
FROM (
    SELECT …, ROWNUM num FROM
        (SELECT … FROM t_client ORDER BY name)
    )
WHERE num BETWEEN 10 and 40
```

   - avec ROW_NUMBER :  

```sql
SELECT …
FROM (
    SELECT …, ROW_NUMBER() OVER (ORDER BY name) num
    FROM t_client
    )
WHERE num BETWEEN 10 and 40
```

>Référence : [http://oracle.developpez.com/faq/?page=3-1#rownum](http://oracle.developpez.com/faq/?page=3-1#rownum)


#### Cas MS SQL Serveur 2005

 Affiche 30 lignes à partir de l'enregistrement 10 :

```sql
SELECT * FROM (
    SELECT TOP 10 Field1, Field2 FROM (
        SELECT TOP 30 Field1, Field2
        FROM  matable
        ORDER BY monchamp asc
    ) AS tbl1 ORDER BY monchamp desc
) AS tbl2 ORDER BY monchamp asc
```

>Référence : [http://sqlserver.developpez.com/faq/?page=Jeu#Jeu1](http://sqlserver.developpez.com/faq/?page=Jeu#Jeu1)

 Autre approche :

```sql
select * FROM (
    select *, row_number() over(order by mon_champ) as rownum from maTable
	) orderedResults where rownum between 10 and 40
```

#### Cas mySQL :

Utilisation de l’instruction `LIMIT` < indice de départ dans la liste de résultat >, < nombre de résultat à afficher >

Exemple, à partir de l’élément 10, afficher 30 résultats :

```sql
SELECT *
FROM `ARCHIVA_ARTIFACT`
LIMIT 10 , 30
```

#### Cas postgreSQL

La syntaxe s’approche de celle de MySQL mais attention car elle est inversée :

```sql
SELECT select_list
FROM table_expression
[LIMIT { number | ALL }] [OFFSET number]
```

Sur le même exemple : à partir de l’élément 10, afficher 30 résultats :

```sql
SELECT *
FROM `ARCHIVA_ARTIFACT`
LIMIT 30 OFFSET 10
```

| Avantages | Inconvénients |
| --------- | ------------- |
| Ne retourne que le resultset de la page souhaitée | on exécute la requête à chaque fois (ou du moins à chaque changement de page) |
| | Dépendant SGBD |
[postgreSQL - paginer - avantages-inconvenients]

### Paginer sur le sgbd (2)

Si une requête est coûteuse en temps d’exécution : utiliser une table partagée par les utilisateurs pour stocker le résultat complet de la requête, ensuite la pagination est faite sur cette table selon l’une des techniques précédentes.

| Avantages | Inconvénients |
| --------- | ------------- |
| on exécute la requête une seule fois | Espace disque plus important, et à priori une table par requête (mais on pourrait ne stocker que les ids du résultat) |
[paginer - sgbd]

### Paginer sur le serveur

| Avantages | Inconvénients |
| --------- | ------------- |
| on exécute la requête une seule fois | Tout le resultset est en session |
| Indépendant SGBD | Pas de modif concurrente |
[paginer - server]

## Types Java spécifiques

Il est parfois utile d’avoir des types spécifiques dans des BO. Par exemple on peut prendre le cas d’une date stockée en tant que varchar dans la base de données et que l’on souhaite traiter de façon particulière en java.

Considérons que nous avons une classe `DateIncomplete` qui correspond à notre type. Les objets métier de notre application vont contenir des variables de type `DateIncomplete`.

Pour que MyBatis puisse gérer correctement ce type de champs indépendamment de la structure de l’objet `DateIncomplete`, il va falloir créer un handler particulier pour ce type de champs qui va étendre `BaseTypeHandler`.

__Exemple :__

```java
    // ExampleTypeHandler.java
    @MappedTypes(DateIncomplete.class)
        public class ExampleTypeHandler extends BaseTypeHandler<DateIncomplete> {

        @Override
        public void setNonNullParameter(PreparedStatement ps, int i, DateIncomplete parameter, JdbcType jdbcType) throws SQLException {
            ps.setString(i, parameter.getValue());
        }

        @Override
        public String getNullableResult(ResultSet rs, String columnName) throws SQLException {
            return rs.getString(columnName);
        }

        @Override
        public String getNullableResult(ResultSet rs, int columnIndex) throws SQLException {
            return rs.getString(columnIndex);
        }

        @Override
        public String getNullableResult(CallableStatement cs, int columnIndex) throws SQLException {
            return cs.getString(columnIndex);
        }
    }
```

Ensuite il suffit dans la configuration de MyBatis de lui préciser que pour tous les champs de type DateIncomplete il faut utiliser le `DateIncompleteTypeHandler`. Ceci s’effectue dans le fichier `mybatis-config.xml`.

```xml
    <!-- mybatis-config.xml -->
    <typeHandlers>
      <typeHandler handler="org.mybatis.example.ExampleTypeHandler"/>
    </typeHandlers>
```


## Spécificités SQL des différents SGBD

Les fonctionnalités avancées d’SQL n’étant pas normalisées, il est nécessaire parfois d’utiliser les syntaxes spécifiques à chaque système de gestion de base de données.

### Retour de la première expression rencontrée non nulle

Voici les équivalents à utiliser selon le SGBD cible :

- Cas oracle :  
    Oracle ne dispose pas de fonction `ISNULL()`. Cependant on peut utiliser la fonction `NVL()` qui effectue la même chose.  
    Exemple :  

```sql
SELECT ProductName,UnitPrice*(UnitsInStock+NVL(UnitsOnOrder,0))
FROM Products
```

- Cas MS SQL serveur 2005 :  

```sql
SELECT ProductName,UnitPrice*(UnitsInStock+ISNULL(UnitsOnOrder,0))
FROM Products
```

- Cas mySQL :  
    MySQL a bien une fonction `ISNULL()`, cependant elle fonctionne légèrement différemment de la fonction `ISNULL()` de Miscrosoft. Pour MySQL, on utilisera donc la function `IFNULL()` à la place. Exemple :

```sql
SELECT ProductName,UnitPrice*(UnitsInStock+IFNULL(UnitsOnOrder,0))
FROM Products
```
    ou bien la fonction `COALESCE()` :

```sql
SELECT ProductName,UnitPrice*(UnitsInStock+COALESCE(UnitsOnOrder,0))
FROM Products
```
- Cas postgreSQL :  
    La fonction `ISNULL()` n’existe pas sous PostgreSQL, on utilisera la fonction `COALESCE()` à la place :

```sql
SELECT ProductName,UnitPrice*(UnitsInStock+COALESCE(UnitsOnOrder,0))
FROM Products
```

### Utilisation des LOBs

Les LOB sont les objets larges (de l’anglais « Large OBject ») soit typiquement des types de données d'environ 4 ko ou plus, bien que certaines bases de données peuvent gérer plus de 32 ko avant que les données deviennent « larges ».

#### Avec PostegreSQL

Pour l’utilisation des LOBs, on veillera à :

- Passer le mode autocommit (activé par défaut) à `false`.
- A ne pas faire de commit, ceci est géré par le `commitOnClose` de la méthode open de `LargeObject`;

Attention, lors de la suppression, le fichier n'est pas supprimé c'est uniquement l'`oid`. Il faut donc supprimer le link. Pour cela, il est possible d'installer un package postgresql qui supprime le link par trigger : `create extension lo`

__Exemple de code d'insertion/update :__

Fichier `sqlMap.xml` (MyBatis) :

```xml
    <update id="updateEmployee" parameterType="com.spring.model.Employee">
        update employee
        <set>
            <if test="fileData.originalFilename != null">filename ={fileData.originalFilename,jdbcType=VARCHAR},</if>
            <if test="fileData.ContentType != null">fileContentType ={fileData.contentType,jdbcType=VARCHAR},</if>
            <if test="fileData.bytes != null">fileData ={fileData.bytes},</if>
        </set>
        where
        empId ={empId}
    </update>
```

__Exemple de code d'récupération/lecture :__

Fichier sqlMap.xml (MyBatis) :

```xml
    <select id="getUploadedFileForEmployee" parameterType="Long"
        resultType="emp">
        select empId,fileName,fileContentType,fileData as
        fileDataBytes
        from
        employee where
        empId=#{empId}
    </select>
```