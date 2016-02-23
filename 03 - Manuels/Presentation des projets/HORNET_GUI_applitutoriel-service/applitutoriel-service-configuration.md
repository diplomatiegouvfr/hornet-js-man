
### Fichiers de configuration de l'application

#### Fichier de configuration du cache pour les accès à la base de données: ehcache.xml

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

#### Fichier de configuration des traces applicatives: logback.xml

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

#### Fichier de configuration d'accès à la base de données: jdbc.properties

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
| jdbc.driver | Driver SQL | org.hsqldb.jdbcDriver |
| jdbc.url | Url de connexion à la BDD|jdbc:hsqldb:mem:APPLITUTORIEL_DB |
| jdbc.username | Utilisateur pour la connexion | sa |
| jdbc.password | Mot de passe de l’utilisateur | |
[Configuration des jdbc - Serveur d’application]

#### Fichier de configuration d'accès au serveur mail : mail.properties  

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
| mail.smtp.host | Serveur de mail | Ex : smtp.serveur.fr |
| mail.smtp.username | Nom du compte utilisateur | |
| mail.smtp.password | Mot de passe du compte utilisateur | |
| mail.debug | Activer ou non les traces de débogage pour l’envoi des mails | true |
[Configuration des mails - Serveur d’application]


#### Fichier de configuration d'accès au serveur mail : mailService.properties  

| Paramètre | Description | Valeur |
|-----------|-------------|--------|
| mail.application.name | Nom de l'application | Applitutoriel |
| mail.application.url | URL de l'application | http://localhost:18080/applitutoriel |
| mail.messageid.domain | nom de domaine pour le MessageID de l'entete SMTP | diffusion.diplomatie.gouv.fr |
| mail.contact.to | Destinataires du mail de contact | "destinataire\@mail.com", "destinataire2\@mail.com" |
| mail.contact.object | Objet du mail de contact | [%s] Un utilisateur de l'application (%s %s) a envoyé un mail de contact |
| mail.contact.body | chemein du template velocity du corps | fr/gouv/diplomatie/applitutoriel/business/service/ModeleMailContact.vm |
[Configuration du service de mail - Serveur d’application]

#### Fichier pour l'accès au web services: webservices.properties

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
