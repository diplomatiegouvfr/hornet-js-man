# Best Practices Java

## Général

- __Ne jamais oublier le premier principe, aphorisme de Donald Knuth :__

> Premature Optimization is the root of all evil.

[http://fr.wikipedia.org/wiki/Optimisation_de_code](http://fr.wikipedia.org/wiki/Optimisation_de_code)

- __Ne pratiquer a priori que les optimisations peu couteuses en développement et les bonnes pratiques suivantes (qui deviendront des réflexes)__
- La signature des fonctions (arguments et type de retour) doit être la plus abstraite possible. En particulier, les collections (`ArrayList`, `HashMap`) devront êtres manipulés sous forme de types abstraits `java.util.Collection`, `java.util.List` ou `java.util.Map`, et non en type concret.
- Ne pas utiliser les classes `Vector` et `Hashtable` qui utilisent de manière cachée la synchronisation Java. Utiliser `ArrayList`, `HashMap`, `TreeMap`, `HashSet`, …  Si la collection DOIT être partagée entre plusieurs Threads, utiliser `Collection.synchronizedCollection(…)`
- Sauf si vous savez exactement à quoi ça sert, n’utilisez pas le mot clé `synchronized`
- Seuls les calculs de montant doivent se faire en `BigDecimal` (et non `Double` ou `Float` pour éviter les erreurs d’arrondi). Pour les identifiants de base de données, le type `Long` suffit.
- Tirer partie du « Just In Time compiler » de la JVM. Le JIT transforme le code Java appelé fréquemment en code natif.  

```java
    public void maFonction() {
        for(int i=0 ; i<100000 ; i++) {
            //traitement
        }
    }
```

en

```java
    public void maFonction() {
        for(int i=0 ; i<100000 ; i++) {
            maFonctionUnitaire(i) ;
        }
    }

    public void maFonctionUnitaire (int i) {
            //traitement
        }
```

- __ Dans le fichier services-rest-servlet.xml de l'archetype Hornet Service :__

Les exports de fichiers ne sont pas pris en compte lors de la construction qui permet de générer un projet maven type. Ce projet contient la configuration et les sources nécessaires à l'implémentation de services REST en Java dans le cadre de l'architecture Hornet JS.

Pour les prendre en compte il faut modifier la ligne 
- `<context:component-scan base-package="${package}.web, hornet.framework.web.conf, hornet.framework.web.controller" />`

par

- `<context:component-scan base-package="${package}.web, hornet.framework.web" />`
