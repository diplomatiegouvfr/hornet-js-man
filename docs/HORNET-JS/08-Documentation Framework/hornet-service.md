# Hornet-service

L'artefact maven hornet-service permet de construire les services REST Java d'une application Hornet JS. Ceux-ci assurent l'accès aux données et aux services métier.

Hornet-service est composé de différents modules :

- hornet-service-bom : permet de gérer de façon centralisée les versions des librairies externes utilisées par le framework Hornet et par les applications Hornet (voir la notion maven ["Bill Of Meterials"](https://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html#Importing_Dependencies))
- hornet-service-parent : artefact parent pour les modules applicatifs
- hornet-service-javadoc-resources
- hornet-service-framework-parent : contient les sous-modules composant le framework Hornet pour la partie services REST
- hornet-service-typescript-maven-plugin : génération de fichier d.ts en fonction des controller

## Modules du framework

### hornet-pom-orm

Ajoute les dépendances vers les librairies permettant l'accès aux données persistantes et le "mapping" (Object Relational Mapping (ORM) Data Access) avec les objets métier Java.

### hornet-pom-reports

Ajoute les dépendances nécessaires pour l'exports de données vers différents formats de sorties tel que Excel pou PDF.

### hornet-service-clamav

Fournit un service (`ClamAVCheckService`) permettant de faciliter la réalisation de tests antivirus avec ClamAV.

### hornet-service-clamavsimulateur

Fournit un serveur permettant de simuler le fonctionnement d'un serveur ClamAV. A utiliser pour réaliser des tests.

### hornet-service-core

Couche technique : fournit les différents niveaux d'exceptions, les services d'export vers différents formats de sortie, un service d'envoi de mail, et quelques classes utilitaires.

### hornet-service-data

Couche technique : fournit des éléments et dépendances pour l'utilisation Spring Data.

### hornet-service-web

Couche technique : fournit des éléments basés sur hornet-service-core ainsi qu'un intercepteur d'exceptions pour les controlleurs web.

### hornet-service-httpparam

Fournit une classe utilitaire (`HTTPClientParameterBuilder`) permettant de faciliter le paramétrage d'un client HTTP.

### hornet-service-metrologiefilter

Fournit un aspect Spring (`MetrologieAspect`) permettant de mesurer les temps d'exécution entre différentes couches techniques.

### hornet-service-typemime

Fournit un utilitaire permettant de déterminer le type MIME d'un contenu.

### hornet-service-webservicehelper

Fournit des classes utilitaires utilisables par les services web, notamment pour la gestion de certificats.

## Utilisation

La construction du paquet hornet-service à partir des sources se fait avec maven via la commande suivante réalisée dans le répertoire hornet-service :

```shell
mvn package
```

# Details d'implémentation (Rappels)

Le BackEnd Java s'organise principalement autour de 3 couches:

Controller : Point d'entrée du BackEnd, qui porte toutes les interaction avec le Frontend.
Service : Service implémentant les règles fonctionnelles et portant l'intelligence des traitements métiers.
Repository et Entity : Couche de persistence des données.

Pour alléger la configuration du poste des développeurs, il est possible de lancer l'application avec Spring Boot à la place du serveur d'application Tomcat. Toutes les dépendances sont tirées, et le point d'entrée est la classe `Application.java`.

## Controller

Point d'entrée de notre backend et est déclenché sur des URI. Ces derniers sont implémentés suivant le standard REST.

Un controlleur est une classe annotée `@RestController`, pouvant préciser la base de l'URI gérée par ce contrôleur avec l'annotation `@RequestMapping`.

Chaque méthode à exposer en REST  doit être publique, annotée`@RequestMapping` pour préciser les path de l'URI et le verbe HTTP la déclenchant.

Ils sont en charge de faire un contrôle de premier niveau sur le contenu dees requêtes et des paramètres passés.
Ils appellent les différents services.

Il est aussi en charge de préciser les status HTTP si le traitement s'est bien passé avec l'annotation ```@ResponseStatus``, par défaut, le status est à 200

```java

    @ResponseStatus(value = HttpStatus.NO_CONTENT)
    @RequestMapping(value = "/{id}", method = RequestMethod.PUT)
    public void modifier(@PathVariable final Long id, @RequestBody final Partenaire partenaire) {

        partenaire.setId(id);
        partenaireService.modifierPartenaire(partenaire);
    }


```
En cas d'erreur, c'est une exception qui est déclenchée et c'est cette dernière qui porte le status avec la même annotation

```java

@ResponseStatus(value = HttpStatus.NOT_FOUND)
public class ObjectNotFoundException extends TechnicalException {

    private static final long serialVersionUID = 7741882800794183366L;

    public ObjectNotFoundException() {
        this("ERR_OBJECT_NOT_FOUND", null);
    }

    public ObjectNotFoundException(final String[] args) {
        this("ERR_OBJECT_NOT_FOUND", args);
    }

    public ObjectNotFoundException(final String code, final String[] args) {
        super(code, args);
    }
}

```

Par défaut, le status est à 500 en cas de déclenchement d'une exception.

Pour plus de détail consultez /hornetshowroom/composant/page/hornet-js/conception/java/controleur

### REST

Les API REST sont basées sur HTTP, elles utilisent les spécifications de ce protocole, ce qui simplifie son appel, un simple accès à une URI.

Les règles fondamentales :
1. L'URI permet d'accéder à un ensemble de ressources ou uniquement à l'une d'elles
2. Le verbe HTTP pour l'opération à effectuer
3. La réponse HTTP représente le résultat de l'opération
4. Le status HTTP donne le code retour et donc l'état de l'opération

#### Les principaux verbes

##### GET

Ce verbe permet de récupérer une ressource ou une collection de ressource.

###### Status HTTP

| Code |	Signification |	Commentaire |
| ---- | ---------------| ----------- |
| 200 |	OK | La ressource ou collection est renvoyée.
| 206 |	Partial content |	La ressource ou collection est renvoyée partiellement
| 400 |	Bad Request |	La requête n’est pas correcte (paramètres incorrects.)
| 404 |	Not Found |	Aucune ressource trouvée.

##### PUT

Ce verbe permet de remplacer une ressource identifiée.

###### Status HTTP

| Code |	Signification |	Commentaire |
| ---- | ---------------| ----------- |
| 200 |	OK | La ressource a été modifiée et est renvoyée dans la réponse HTTP.
| 204 |	No content |	La ressource a été modifiée
| 404 |	Not Found |	La ressource à remplacer n'a pas été trouvée.

##### DELETE

Ce verbe permet de supprimer une ressource identifiée.

###### Status HTTP

| Code |	Signification |	Commentaire |
| ---- | ---------------| ----------- |
| 200 |	OK | La ressource a été supprimée et est renvoyée dans la réponse HTTP.
| 204 |	No content |	La ressource a été supprimée
| 404 |	Not Found |	La ressource à supprimer n'a pas été trouvée.

##### POST

Ce verbe permet de créer, d'ajouter une ressource.

###### Status HTTP

| Code |	Signification |	Commentaire |
| ---- | ---------------| ----------- |
| 201 |	Created | La ressource a été crée et est renvoyée dans la réponse HTTP.
| 204 |	No content |	La ressource a été supprimée
| 400 |	Bad Request |	La requête n’est pas correcte

##### PATCH

Ce verbe permet de modifier partiellement une ressource identifiée. Le corps de la requête ne comporte que les attributs à modifier.

###### Status HTTP

| Code |	Signification |	Commentaire |
| ---- | ---------------| ----------- |
| 200 |	OK | La ressource a été modifiée et est renvoyée dans la réponse HTTP.
| 204 |	No content |	La ressource a été modifiée
| 404 |	Not Found |	La ressource à modifier n'a pas été trouvée.

## Service

C'est un service Spring annoté avec `org.springframework.stereotype.Service`. Il comporte la logique métier à implémenter. C'est lui qui fait appel à la couche de persistence.

## Repository

C'est la couche de persistence des données, elle est basée sur Spring Data JPA (https://docs.spring.io/spring-data/jpa/docs/current/reference/html/), et s'appuie sur les ```Entity``` JPA qui dont des beans avec des annotations JPA (au minimum ```@Entity``` sur la classe). Ce framework nous fournit plusieurs interfaces de marquage, la plus simple est org.springframework.data.repository.Repository.

Afin d'alléger l'implémentation des entités, nous utilisons le projet Lombok. Ce projet met à disposition des annotations pour générer les Getter, Setter, ...
Pour l'utilisation sous Eclipse vous devez rajouter la ligne suivante dans le fichier `eclipse.ini`:

```

-javaagent:/home/${MonUser}}/.m2/repository/org/projectlombok/lombok/1.16.18/lombok-1.16.18.jar

```

Vous trouverez  la documentation à l'adresse suivante : https://projectlombok.org/features/all

### org.springframework.data.repository.Repository

Son fonctionnement est assez simple, il se base sur le nom de la méthode pour générer la requête :

```java

public interface CiviliteRepository extends Repository<Civilite, Long> {

    // Récupération de toutes les Civilité
    List<Civilite> findAll();

    // Récupération d'une civilite par le libelle
    Civilite findByLibelleIgnoreCase(final String libelle);
}

```

Le nom des méthodes peuvent intégrer des notions de tri, de comparaison, de limite ou de pagination. Pour plus de détails, consulter https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.query-methods

Si besoin et pour tous les types de repository, il est possible de redéfinir la requête à la place de laisser le framework la calculer et ce grace à l'annotation @Query ou @NamedQuery


### d'autres repository

org.springframework.data.repository.CrudRepository : Methode CRUD
org.springframework.data.repository.PagingAndSortingRepository : Etend le CrudRepository et ajoute des méthodes de findAll avec tri ou pagination
org.springframework.data.jpa.repository.JpaSpecificationExecutor : Méthode de recherche avec des spécifications
hornet.framework.repository.CrudProjectionRepository : Etend le CrudRepository et ajoute des méthodes de recherche avec spécifications et projections

### Les projections

A la place de retourner des instance d'entité complète et potentiellement toute leur grappe, il est possible d'utiliser des projections, ce qui nous permet de filtrer que certains attributs d'une entité. Le cas d'utilisation serait de renvoyer un résumé pour les résultats de recherches et un détail pour l'édition. Ces projections peuvent être statiques (défini dans le retour de la méthode) ou dynamiques (type génériques sur le retour et paramêtre de la méthode.

```java

// statique
List<FullName> findByNom(String lastname);

//dinamique
List<T> findByNom(String lastname, Class<T> type);

```

Ces projections sont de simples interfaces définissant les getter des attributs à conserver. Grâce à l'annotation @Value, nous pouvons ajouter des attributs particuliers par rapport à l'entité et qui serait le cummul de deux attributs par exemple.


```java

interface FullName {

  @Value("#{target.nom + ' ' + target.prenom}")
  String getNomComplet();
}

```

A la place de l'annotation, il est possible d'utiliser les `default` méthodes

```java

interface FullName {

  String getNom();
  String getPrenom();

  default String getNomComplet() {
    return getNom.concat(" ").concat(getPrenom());
  }
}

```

Mais dans ce cas là, l'objet retourné aurait aussi le nom et le prénom, contrairement à l'exemple d'avant.

Pour plus de détails, consultez https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#projections


### les spécifications

Les spécifications sont l'implémentation des Critéria JPA, ils permettent donc de générer les clauses de sélection (WHERE) pour nos requêtes


```javascript
public class SecteurSpecification extends SpecificationUtils<Secteur> {

    public static Specification<Secteur> NomOrDescOrIdIfDefined(final String nom, final String description,
                final Long id) {

        Specification<Secteur> specification = (root, query, builder) -> null;

        if (nom != null) {
            specification = specification.and(SecteurSpecification.upper("nom", nom.toUpperCase()));
        }
        if (description != null) {
            specification = specification.and(SecteurSpecification.upper("descr", description.toUpperCase()));
        }
        if (id != null) {
            specification = specification.and(SecteurSpecification.equal("id", id));
        }

        return specification;

    }
```
