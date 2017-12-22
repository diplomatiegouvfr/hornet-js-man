# DAO
## Couche DAO

Les bonnes pratiques sont :

- Cibler les requêtes optimisées sur les fonctionnalités les plus utilisées… Le N+1 select sera souvent acceptable, et bien moins couteux à développer.
- On peut récupérer les associations 1-1 et 1-N par une seule requête avec jointure et mapper le résultat sur un BO (via plusieurs `resultMap` MyBatis).
- Ne rapporter qu’une collection rattachée à un objet par requête (`left join`). Si un objet possède plusieurs collections, il est plus optimal de récupérer chaque collection séparément.
- Pour les traitements par lots, on pensera à utiliser la pagination lors de la lecture des objets à traiter pour optimiser la mémoire, et les Batchs JDBC (via MyBatis) pour optimiser les écritures.

## Base de données

Les bonnes pratiques sont :

- Pas de procédure stockée, sauf si problème de performance en exploitation qui impose d’en créer.

##	Horloges et horodatage

Il est fréquent d’avoir à horodater des étapes d’un traitement.

Certaines règles sont importantes à suivre pour éviter les incohérences, vu que les horloges des différents serveurs (application, base de données …) peuvent être légèrement décalées (bien que les serveurs de production soient synchronisés via le protocole NTP).

Les bonnes pratiques sont :

- Le serveur d’application est le serveur de référence pour les horodatages,
- Il ne faut pas utiliser une échelle de temps inférieure à la milliseconde,
- Il faut préférer un ordre d’étape à un ordre chronologique.