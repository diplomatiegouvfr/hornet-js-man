# Livraison des projets Hornet sur le svn du MAE

## Spécificités des projets Hornet

Les projets hornet sont constitués de deux parties : 

- Partie client basée sur `nodejs` 
- Partie service basée sur `java` 

Chacune des composantes possède un manager de package :

- nodejs : `hornetBuilder` basé en parti sur NPM
- java : `maven`

L'objectif de ce document est de fournir la procédure avant livraison sur le `SVN` du MAE. 

## Partie Client

Pour la partie client, il est important de vérifier les points suivants avant livraison : 

- Non présence de version non fixée dans la package.json, c'est à dire contenant les caractères `~,^`, ex : `^1.0.0`
- La version du module 
- Vérifier les configurations, `ex : ./config/default.json`
- Ne pas commiter les .js et .jsmap quand il s'agit de fichier typescript `.ts`
- Ne pas commiter les node_modules
- Ne pas publier les chunks webpack présent dans le répertoire `./static/js`

## Partie Service

Pour la partie service, il est important que les versions contenues dans le `trunk`ne contiennent plus de version `SNAPSHOT`.

Procédure de `tag` : le MAE recommande de réaliser le `tags-integration` à partir du trunk, puis de modifier les pom.xml contenant des `SNAPSHOT`.

Rappel : les `tags-integration` sont les versions potentiellements mises à disposition en tag.
D'où l'importance de ne plus avoir cette notion de `SNAPSHOT`.

Ensuite, incrémenter la version mineur ou majeur du `pom.xml` sur le trunk pour les prochaines versions.


## Partie RUNDECK - DEPAUT

Afin de faciliter le déploiement automatisé, il est nécessaire de préparer des templates de configuration.

Ces templates de configuration doivent impérativement être indépendants d'un environnement.

Pour construire un template de configuration, voici les quelques conventions utilisées:

* Un fichier template contient des caractères compatible UTF-8.
* Un fichier binaire ne peux pas servir de template.

Une zone variable dans un template est représenté par la syntaxe suivante:

* Prefix de zone: __#{__
* Nom de variable: __nomDeLaZoneVariable__ - Les caractères point et space ne sont pas permis dans un nom de zone variable.
* Suffix de zone: __}__
* Exemple: __#{nomDeLaZoneVariable}__
	
* Une zone variable est complètement substituable par la valeur de la variable associée.

*Exemple de l'application : applitutoriel-service (partie tomcat)*

```bash

	 #-------------------------------------
	 # name: jdbc.properties, author: MAE, version: 1.0
	 #-------------------------------------
	 # Configuration du composant jdbc
	 #-------------------------------------
	 jdbc.driver#{JDBC_DRIVER}
	 jdbc.url#{JDBC_URL}
	 jdbc.username#{JDBC_USERNAME}
	 jdbc.password#{JDBC_PASSWORD}
	 jdbc.validationQuery#{JDBC_VALIDATION_QUERY}

```
En complément, un fichier de propriétés spécifiques à l'environnement cible est à fournir lors d'une déploiement automatisé. Ce fichier recense l'ensemble des zones variables des fichiers templates.

Notre moteur de template sert à remplacer les expressions `{valeur}` d'un texte par des valeurs. Ces valeurs sont renseignées soit dans un fichier `*.properties` et/ou soit dans des `variable d'environnement système`.

Voici un exemple de liste des `variables/valeurs` de configuration, groupées par fichier destination. Chaque variable peut être reférencée dans les templates source. Un script shell remplace les variables dans chaque template et produit le fichier de configuration correspondant.

*Exemple de l'application : applitutoriel-js (partie nodejs)*

``` bash

	#################
	# production.json
	#################
	APPLICATION_CONTEXT_ROOT=applitutoriel-js
	THEME_HOST=#{URL_THEME_HOST}
	THEME_URL=#{URL_THEME}
	
	#exemple :
	CAS_ENABLED=false
	
	# Variables supplémentaires à déclarer si besoin :
	LOGCLIENT_LEVEL=INFO
	
	#repertoire vers la commande node
	NODE_BIN_DIR=/usr/bin
	
	#Remplacer l'adresse du serveur Tomcat fournissant la partie service.
	DEFAULT_SERVICE_HOST=#{URL_DEFAULT_SERVICE_HOST}
	#Remplacer le contexte racine de l'application tomcat fournissant la partie service.
	SERVICE_CONTEXT_ROOT=applitutoriel-service


```
*Exemple de l'application : applitutoriel-service (partie tomcat)*

``` bash

	#################
	# JDBC.PROPERTIES
	#################
	# cas d'utilisation d'une base HSQLDB (HyperSQL DataBase) en memoire
	jdbc_driver="org.hsqldb.jdbcDriver"
	jdbc_url="jdbc:hsqldb:mem:APPLITUTORIEL_DB"
	jdbc_username="sa"
	jdbc_password=""
	jdbc_validationQuery="SELECT 1 FROM INFORMATION_SCHEMA.SYSTEM_USERS"
	
	# cas d'utilisation d'une base PostgreSQL
	#jdbc_driver="org.hsqldb.jdbcDriver"
	#jdbc_url="jdbc:hsqldb:mem:APPLITUTORIEL_DB"
	#jdbc_username="sa"
	#jdbc_password=""
	#jdbc_validationQuery="SELECT 1"
	
	
	#######################
	# WEBSERVICE.PROPERTIES
	#######################
	tomcat_conf_dir="${DEPAUT_TOMCAT_CONF_DIR}"
	
	# propriétés pour le KeyStore
	#applitutoriel_keyStore="${DEPAUT_TOMCAT_CONF_DIR}/keystore/client.ks"
	applitutoriel_keyStorePassword="client"
	applitutoriel_aliasCertificat="client"
	
	# propriétés pour le TrustStore
	#applitutoriel_trustStore="${DEPAUT_TOMCAT_CONF_DIR}/keystore/client_truststore.jks"
	applitutoriel_trustStorePassword="client"
	
	# endpoint du webserviceSpringObjectFactory
	applitutoriel_HelloService_endpoint="https://localhost:8443/mockHelloServiceSoapBinding"
	
	
	####################
	# LOGBACK-HORNET.XML
	####################
	logback_repertoire="${DEPAUT_TOMCAT_LOG_DIR}"
	logback_nomFichier="applitutoriel-service.log"
	logback_nomFichierRolling="applitutoriel-service.%d{yyyy-MM-dd}.%i.log"

```

Les fichiers de `*.properties` ainsi que les `templates` doivent être fournis sous format `*zip`.

Les `templates` sont copiés sur le serveur apache afin d'être accessibles par le serveur `Rundeck` :

		/var/www/depaut/config/[NOM_PROJET]
		
*Convention de nommage mise en place:*

		[NOM_MODULE]-config-[ENVIRONEMENT_CIBLE].zip