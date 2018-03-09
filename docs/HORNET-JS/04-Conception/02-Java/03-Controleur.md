# Partie contrôleur

## Implémentation d'un contrôleur

* Créer une classe annotée `@Controller`
* Déclarer si besoin la base de l'URI gérée par ce contrôleur avec l'annotation `@RequestMapping`
* Déclarer des méthodes qui seront exposées en REST :
  * avec une visibilité publique
  * annotées par `@RequestMapping`

### Méthode exposée en REST

Une méthode exposée en REST peut être configurée de la manière suivante :

- L'URI écoutée : attribut `value` de l'annotation `@RequestMapping`  
  Cette URI peut contenir des paramètres qui seront passés à la méthode.  
  Exemple de valeur : "/{id}"
- La méthode HTTP écoutée : attribut `method` de l'annotation `@RequestMapping`  
  Exemple de valeurs : GET / POST / UPDATE / DELETE
- Les variables attendues en entrées :
  * Avec l'annotation `@PathVariable` : la variable est récupérée de l'URI
  * Avec l'annotation `@RequestBody` : la variable est récupérée du body de la requête HTTP
  * Sans annotation : il s'agit d'une variable qui sera injectée par Spring  
    Exemples de variables pouvant être injectées par Spring : request, response...
- Le statut HTTP retourné, avec l'annotation `@ResponseStatus`  
  Exemple de valeurs : OK (200), CREATED, NO CONTENT, BAD REQUEST...
- Le contenu retourné :
  * avec l'annotation `@ResponseBody` : indique que l'objet retourné doit être placé dans le body de la réponse HTTP. 
   Permet à Spring de déclencher automatiquement la sérialisation en JSON.
  * `void` si la méthode n'a rien à retourner.  
    Dans ce cas il est obligatoire de préciser le statut de retour (`@ResponseStatus`) pour ne pas générer d'erreur Spring.

Exemple :

```java
    @Controller
    @RequestMapping("/partenaires")
    public class PartenaireController {

        @Resource
        private PartenaireService partenaireService;

        @RequestMapping(value = "/{id}", method = RequestMethod.GET)
        public @ResponseBody Partenaire lire(@PathVariable final Long id) {

            final Partenaire partenaire = partenaireService
                    .lirePartenaire(id);
            return partenaire;
        }

        @RequestMapping(value = "", method = RequestMethod.GET)
        public @ResponseBody ITreeNode lister() {

            final ITreeNode tree = partenaireService
                    .listerArborescenceOrganismePartenaire();
            return tree;
        }

            @ResponseStatus(HttpStatus.CREATED)
        @RequestMapping(value = "", method = RequestMethod.POST)
        public @ResponseBody Partenaire ajouter(
                @RequestBody final Partenaire partenaire) {

            final Partenaire ajout = partenaireService
                    .ajouterPartenaire(partenaire);
            return ajout;
        }

        @ResponseStatus(value = HttpStatus.OK)
        @RequestMapping(value = "/{id}", method = RequestMethod.PUT)
        public void modifier(@PathVariable final Long id,
                @RequestBody final Partenaire partenaire) {

            partenaire.setId(id);
            partenaireService.modifierPartenaire(partenaire);
        }

        @ResponseStatus(HttpStatus.NO_CONTENT)
        @RequestMapping(value = "/{id}", method = RequestMethod.DELETE)
        public void supprimer(@PathVariable final Long id) {

            partenaireService.supprimerPartenaire(id);
        }

        @ResponseStatus(value = HttpStatus.OK)
        @RequestMapping(value = "/suppression", method = RequestMethod.POST)
        public void supprimerEnMasse(@RequestBody final List<Long> listeIds) {

            LOG.debug("Demande de supression en masse");
            for (final Long id : listeIds) {
                LOG.debug("Suppression du partenaire d'id {}", id);
                supprimer(id);
            }
        }
    }
```

## Configuration du DispatcherServlet

Le fichier xml de configuration du `DispatcherServlet` doit contenir à minima les lignes suivantes :

- Utilisation du mode annotation :  

```xml
    <mvc:annotation-driven />
```

- Scan des controlleurs déclarés par l'annotation `@Controller` :  

```xml
    <context:component-scan
        base-package="fr.gouv.diplomatie.applitutoriel.web.controller" />
```  

Le package déclaré ici doit être modifié pour correspondre à l'application courante.

Il est possible de déclarer plusieurs packages en les séparant par une virgule.

- Forçage de tous les échanges en mode JSON / UTF-8 :  

```xml
    <bean class="org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter">
    	<property name="messageConverters">
            <list>
                <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                    <property name="supportedMediaTypes">
                        <list>
                            <value>application/json; charset=UTF-8</value>
                        </list>
                    </property>
                </bean>
            </list>
        </property>
    </bean>
```  

  Cela permet d'éviter d'avoir à préciser le format attendu (JSON) et l'encodage attendu (UTF-8), sur chacune des méthodes REST des contrôleurs.

## <a id="gestionnaire_erreur"></a>Utilisation du gestionnaire d'erreur

Lors du traitement d'une requête, si une erreur survient dans le contrôleur, ou dans un des services appelés, et que cette erreur n'est pas gérée alors elle remonte jusqu'au tomcat et génère une trace d'erreur au format HTML.

Ce comportement n'est pas adapté à un client REST qui ne pourra pas utiliser les informations affichées au format HTML.

Le framework dispose d'un intercepteur d'erreur permettant de remonter les erreurs survenues pendant les traitements d'une requête, vers le client REST à travers un DTO approrié, qui sera sérialisé au format JSON.

Ce composant est présent dans le module `Hornet-service-web` : `GlobalDefaultExceptionHandler`.
Il gère les exceptions suivant deux types :

  - `BusinessException` et `BusinessListException`
  - `Exception`


Chaque type d'exception peut porter le status HTTP à renvoyer avec l'utilisation de annotation `@ResponseStatus` sur la classe d'exception. Si la classe ne comporte pas cette annotation, le status HTTP sera valorisé à 500.

Pour activer ce gestionnaire d'erreur dans une application Hornet :



- Vérifier la présence du module _hornet-service-web_ dans les dépendances Maven
- Ajouter le scan du package _hornet.framework.web.controller_ dans la configuration du DispatcherServlet :

```xml
    <context:component-scan
            base-package="fr.gouv.diplomatie.applitutoriel.web.controller,
		          hornet.framework.web.controller" />
```


### Flux JSON pour BusinessException et BusinessListException

L'exception `BusinessListException` est un conteneur de `BusinessException`, pour permettre de renvoyer une liste de ces dernières avec un seul appel (traitement de masse, validation de plusieurs règles de gestion...).

Le flux renvoyé aura toujours la même structure quel que soit le type, car une `BusinessException` est convertie en `BusinessListException` ne contenant qu'une seule `BusinessException`. 

En retour nous aurons les informations suivantes :

- url : url qui a généré le ou les exception(s).
- Exs : liste des `BusinessException` :
    - code : code de la `BusinessException` (exemple ER-PA-RPA-03)
    - args : le(s) argument(s) pouvant servir à la contruction du message côté client (exemple ['nom', 'prenom'])

  
### Flux JSON pour Exception

Le flux renvoyé au client contient les informations suivantes :

- name : nom de la classe de l'exception
- code : id unique d'erreur (timestamp)
- message : message de l'exception
- stackTrace : pile d'appels
