# Applitutoriel-service

applitutoriel-service est la partie service (JEE) de l'application exemple basée sur Hornet 5. 

## Deploiement serveur

### Pré-requis

- tomcat 8.X
- maven 3.3.9
- openJdk 8
- hornet-service 5.x.x 

### Installation

- Récupérer le projet applitutoriel-service
- Lancer la commande de construction 

``` shell
mvn package
```

- Dézipper les fichiers de configuration présents dans l'archive `applitutoriel-service-5.x.x-environment.zip`.
- Déployer l'application dans un conteneur Tomcat 8, en prenant garde que la variable `conf/applitutorielprop` du fichier `context.xml` de l'application, référence bien le chemin des fichiers de configuration dézippés à utiliser suivant l'environnement. 

### Vérification

Pour cela, il suffit de faire appel à un service REST depuis un navigateur exemple : `http://localhost:8080/applitutoriel-service/produits`

## Démarrage Spring Boot poste de Dev

### Pré-requis

- maven 3.3.9
- openJdk 8
- hornet-service 5.x.x 
- Eclispe
### Installation

- Récupérer le projet applitutoriel-service
- Ajouter la ligne suivante dans le fichier `eclipse.ini` pour activer l'annotation processing avec Lombok :

```
-javaagent:/home/mouliwi/.m2/repository/org/projectlombok/lombok/1.16.18/lombok-1.16.18.jar

```
- Sous votre IDE, importer le projet maven
- Lancer la commande d'installation des dépendances avec l'IDE ou la commande

``` shell
mvn install -Pdev-spring-boot
```


- Lancer la classe `fr.gouv.diplomatie.applitutoriel.Application` comme Java Application. Cette classe est le point d'entrée. Un serveur Web sera lancé

ou 

``` shell
mvn spring-boot:run -Pdev-spring-boot
```

### Vérification

Pour cela, il suffit de faire appel à un service REST depuis un navigateur exemple : `http://localhost:8080/applitutoriel-service/produits`

### Customisation

Le port et le context path peuvent être modifier et sont configuré dans le fichier `application.properties`

# Configuration

## Fichiers de configuration de l'application

### Cache pour les accès à la base de données: ehcache.xml

L'applitutoriel-service a mis en place un exemple d'utilisation de cache `ehcache` pour les accès aux données dans le DAO :   

- `fr.gouv.diplomatie.applitutoriel.integration.dao.SecteurDAO`

```xml
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="../src/config/ehcache.xsd" updateCheck="false"
	monitoring="autodetect" dynamicConfig="true" name="HORNET_CACHE_MANAGER_DEV">
	<diskStore path="java.io.tmpdir/ehcache" />
	<defaultCache maxEntriesLocalHeap="0" eternal="false" statistics="true" 
		timeToIdleSeconds="1200" timeToLiveSeconds="1200">
		<persistence strategy="localTempSwap" />
	</defaultCache>
	<cache name="secteurcache" maxEntriesLocalHeap="1" maxEntriesLocalDisk="0" 
		eternal="true" statistics="true">
		<persistence strategy="localTempSwap" />
	</cache>
</ehcache>
```

### Traces applicatives: logback.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration scan="true">
	<property name="repertoire" value="/var/log/tomcat8/applitutoriel-service" />
	<property name="nomFichier" value="applitutoriel-service.log" />

	<appender name="FILE" class="ch.qos.logback.core.FileAppender">
		<file>${repertoire}/${nomFichier}</file>
		<append>true</append>
		<encoder>		
			<pattern>%date{ISO8601}|%t|%X{tid:-NO_TID}|%X{user:-NO_USER}|%p|%c|%m %ex{full}%n</pattern>
		</encoder>
	</appender>

	<root level="INFO">
		<appender-ref ref="FILE" />
	</root>

	<!-- definition des logs applicatifs -->
	<logger name="fr.gouv.diplomatie" level="INFO" />

	<!-- definition des logs HORNET -->
	<logger name="hornet" level="INFO" />

	<!-- definition des logs SPRING -->
	<logger name="org.springframework" level="ERROR" />

	<!-- definition des logs IBATIS et JDBC -->
	<logger name="java.sql.Statement" level="ERROR" />
	<logger name="java.sql.ResultSet" level="ERROR" />
	<logger name="java.sql.Connection" level="ERROR" />
	<logger name="java.sql.PreparedStatement" level="ERROR" />
	
	<!-- definition des logs ehcache -->
	<logger name="net.sf.ehcache" level="ERROR" />
</configuration>
```

### Accès à la base de données: jdbc.properties

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
| jdbc.driver | Driver SQL | org.hsqldb.jdbcDriver |
| jdbc.url | Url de connexion à la BDD|jdbc:hsqldb:mem:APPLITUTORIEL_DB |
| jdbc.username | Utilisateur pour la connexion | sa |
| jdbc.password | Mot de passe de l’utilisateur | &nbsp; |
[Configuration des jdbc - Serveur d’application]

### Accès au serveur mail : mail.properties

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
| mail.smtp.host | Serveur de mail | Ex : smtp.serveur.fr |
| mail.smtp.username | Nom du compte utilisateur | &nbsp; |
| mail.smtp.password | Mot de passe du compte utilisateur | &nbsp; |
| mail.debug | Activer ou non les traces de débogage pour l’envoi des mails | true |
[Configuration des mails - Serveur d’application]


### Accès au serveur mail : mailService.properties

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
| mail.application.name | Nom de l'application | Applitutoriel |
| mail.application.url | URL de l'application | http://localhost:18080/applitutoriel |
| mail.messageid.domain | Nom de domaine pour le MessageID de l'entete SMTP | |
| mail.contact.to | Destinataires du mail de contact | "destinataire\@mail.com", "destinataire2\@mail.com" |
| mail.contact.object | Objet du mail de contact | [%s] Un utilisateur de l'application (%s %s) a envoyé un mail de contact |
| mail.contact.body | Chemin du template velocity du corps | |
[Configuration du service de mail - Serveur d’application]

### Accès au web services: webservices.properties

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
| applitutoriel.keyStore | Chemin vers le fichier de keystore | **/etc/ssl/certs/client.ks** |
| applitutoriel.keyStorePassword | Mot de passe pour le keystore | Client |
| applitutoriel.aliasCertificat | Alias sur le certificat | client |
| applitutoriel.algoKeyStore | Algorithme utilisé pour le keystore | SunX50 |
| applitutoriel.typeKeyStore | Type de clé pour le keystore | JKS |
| applitutoriel.trustStore | Chemin vers le fichier de truststore | **/etc/ssl/certs/client_truststore.jks** |
| applitutoriel.trustStorePassword | Mot de passe pour le truststore|client|
| applitutoriel.algoTrustStore | Algorithme utilisé pour le truststore | SunX509 |
| applitutoriel.typeTrustStore | Type de clé pour le truststore | JKS |
| applitutoriel.HelloService.endpoint| Endpoint du webservice HelloService | Exemple : <br/>https://localhost:8443/mockHelloServiceSoapBinding |

Attention l’application ne peut pas démarrer si les chemins vers les fichiers keyStore et trustStore ne sont pas correctement configurés.
