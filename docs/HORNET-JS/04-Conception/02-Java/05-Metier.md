# Partie Métier

## Services

Les services représentent la logique métier de l’application. Ils doivent être réutilisables au sein de l’application (partage entre domaines fonctionnels), mais également entre applications (au travers d’une couche Web-Service par exemple).

- Les services sont implémentés avec des classes JAVA simples. Ils possèdent obligatoirement une interface JAVA.
- Les services peuvent effectuer des traitements simples ou complexes en réutilisant d’autres services (internes à l’application, ou externe).
- Les services collaborent avec les autres services uniquement via leur interface. C’est Spring qui est chargé de fournir l’implémentation appropriée.
- Les services collaborent avec les DAO uniquement via leur interface. Spring est chargé de fournir l’implémentation appropriée
- Les services sont généralement des singletons. Ils ne doivent donc pas contenir de données membres constituant un état sinon celles-ci conserveront leurs valeurs d’initialisation d’un appel à l’autre (voir exemple).
- Les services sont décrits dans le fichier Spring ```spring-appContext-common-service.xml```

__Exemple de service contenant une donnée membre qui constitue un état :__

```java
    public class MonServiceImpl {
        /** <code> maListeErreurs </code> the maListeErreurs     */
        private List<HistoriqueBO> maListeErreurs;

        /** <code>daoHistorique</code> the daoHistorique     */
        private HistoriqueDAO daoHistorique;

        /** <code>daoPurge</code> the daoPurge     */
        private PurgeDAO daoPurge;

        /**
         * @param id identifiant d'une boite aux lettres
         * @return la liste des historiques
         */
        public List<HistoriqueBO> doTraitement(String id) {
            List <HistoriqueBO> liste = new ArrayList<HistoriqueBO>();
            HistoriqueBO historique = new HistoriqueBO();
            historique.setIdBal(id);
            liste = this.daoHistorique.selectHistoriqueByIdBal(
                                    historique.getHistoriqueVO());
            for (HistoriqueBO histo : liste) {
                if (histo.getHisAction() == null) {
                    //Ajout d’elements à un objet membre d’un singleton
                    this.maListeErreurs.add(histo);
                }
            }
            liste.removeAll(this.maListeErreurs);
            return liste;
        }

        /**
         * @return liste des purges
         */
        public List<Purge> doSomethingElse() {
            // utilisation de la liste du singleton dont le contenu peut être
            // modifié au moment de sa lecture par simple appel à la méthode
            // doTraitement
            for (HistoriqueBO histo : this.maListeErreurs) {
                Purge maPurge = new Purge();
                maPurge.setPurIdbal(histo.getIdBal());
                maPurge.setPurDatepurge(new Date());
                this.daoPurge.insert(maPurge);
            }
            PurgeExample example = new PurgeExample();
            example.setOrderByClause("DATE_PURGE");
            return this.daoPurge.selectByExample(example);
        }
    }
```

__Remarque__ : Pour cette exemple il est conseillé de supprimer la donnée membre `maListeErreurs` du service et de passer cet objet liste en paramètre des fonctions `doTraitement` et `doSomethingElse`.

__Exemple de déclaration de service utilisant un autre service :__

```xml
    <bean id="EntrepriseService" class="hornet.projet.business.service.EntrepriseServiceImpl">
        <constructor-arg ref="EntrepriseDAO" index="0" />
        <constructor-arg ref="ReferenceService" index="1" />
    </bean>

    <bean id="ReferenceService" class="hornet.projet.business.service.ReferenceServiceImpl">
        <constructor-arg ref="ReferenceDAO" index="0"/>
    </bean>
```

## Ambiguïtés sur les instanciations

L’utilisation de l’injection de dépendance par constructeur peut mener à des ambiguïtés.

Prenons un exemple :

```java
    public class UnObjet {
        public UnObjet(String str, int entier) {
            …
        }
    }
```

Avec la configuration Spring suivante :

```xml
    <bean id="UnObjet" class="com.example.UnObjet">
        <constructor-arg value="string1" />
        <constructor-arg value="123" />
    </bean>
```

Les valeurs fournies ici pour les arguments du constructeur ne permettent pas à Spring de les différencier. Chacune pourrait potentiellement représenter un nombre ou une chaîne.

Afin de lever l’ambiguïté, il est possible de spécifier le type des arguments :

```xml
    <bean id="UnObjet" class="com.example.UnObjet">
        <constructor-arg value="string1" type="java.lang.String" />
        <constructor-arg value="123" type="int"/>
    </bean>
```

Une autre solution consiste à renseigner le nom de l’argument associé à la valeur ou à la référence :

```xml
    <bean id="UnObjet" class="com.example.UnObjet">
        <constructor-arg value="string1" name="str" />
        <constructor-arg value="123" name="entier"/>
    </bean>
```

Cette méthode n’est cependant pas fiable car elle fonctionne uniquement si le code source Java est compilé avec le flag `debug` activé. Dans ces conditions, il est tout à fait envisageable que cette configuration soit correcte pour les tests en développement mais provoque une erreur lors du déploiement si le flag `debug` est désactivé pour la génération du war.

Pour rendre la configuration Spring indépendante de la méthode de compilation, l’attribut `index` (commençant à 0) du tag `constructor-arg` est donc à privilégier :

```xml
    <bean id="UnObjet" class="com.example.UnObjet">
        <constructor-arg value="string1" index="0" />
        <constructor-arg value="123" index="1"/>
    </bean>
```

Même dans le cas de l’utilisation d’un seul argument, l’index doit être précisé afin de lever toute ambiguïté sur son utilisation.

Pour plus de détails, se référer à la [documentation en ligne de Spring](http://docs.spring.io/spring/docs/4.0.x/spring-framework-reference/html/beans.html#beans-factory-collaborators) sur ce sujet.


En plus de palier à ce type d’erreurs, l’activation du flag de "debug" pour la compilation des sources permet également de rajouter des informations supplémentaires en cas de dysfonctionnement. Ces éléments permettent de faciliter le diagnostic en cas d’incident.

## Services Spring et scope

Par défaut, un service Spring est un singleton, la valeur par défaut de l’attribut scope étant `singleton` :

```xml
    <bean id="monService" class="hornet.projet.business.service.ReferenceServiceImpl" scope="singleton">
        <constructor-arg ref="ReferenceDAO" index="0"/>
    </bean>
```

Singleton signifie qu’une seule instance (ou bean) sera utilisée pour toutes les références à `monService`.

Les autres valeurs possibles pour scope dans une application web sont :

- prototype : une instance est créée pour chaque référence au bean,
- request : chaque requête http possède sa propre instance du bean,
- session : chaque session  http possède sa propre instance du bean.

Dans la majeure partie des cas, le scope singleton est celui à privilégier, chaque service devant impérativement être "thread safe" (permettre et gérer correctement les accès concurrents réalisés par plusieurs threads).

Dans certains cas, les classes et beans nécessaires au fonctionnement d’un service Spring  ne sont pas thread safe et des nouvelles instances doivent impérativement être  créées à chaque appel.

Un pattern possible est alors :

- Déclarer le bean non « thread safe » en prototype (« request » peut aussi convenir) :

```xml
    <bean id="monBeanNonThreadSafe" class="hornet.projet.business.BeanNonThreadSafeImpl" scope="prototype">
    </bean>
```

- Déclarer le bean de service en y ajoutant la définition `lookup-method`

```xml
    <bean id="monBeanService" class="hornet.projet.business.BeanThreadSafeImpl">
        <look-up name="getBeanNonThreadSafe" bean="monBeanNonThreadSafe">
    </bean>
```

- Déclarer la méthode abstraite dans la classe appelante  

```java
    public class BeanThreadSafeImpl {
        private abstract BeanNonThreadSafe getBeanNonThreadSafe();

        public void travail() {
            getBeanNonThreadSafe().faitQuelqueChose() ;
        }
    }
```

## Business Objets (BO)

- Les BO représentent le modèle métier de l’application.
- Un BO est une classe JAVA simple sans dépendance sur l’architecture technique. Il possède au moins un constructeur par défaut et chaque propriété est accessible via des getter/setter.
- Un BO est constitué de propriétés simples, mais peut aussi étendre ou agréger d’autres BO.
- Un BO peut être construit à partir d’un unique VO (mapping un vers un) ou d’un ensemble de VO provenant éventuellement de sources différentes (mapping un à plusieurs). La conversion entre BO et VO sera toujours effectuée dans la couche service.
- Un BO peut contenir de la logique métier, mais ne doit pas faire appel à la couche service, dao, accéder à d’autres ressources, …
