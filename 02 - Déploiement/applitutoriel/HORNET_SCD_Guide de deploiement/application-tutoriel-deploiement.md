# Déploiement

Ce chapitre décrit les différentes étapes nécessaires au déploiement des deux modules de l’application applitutoriel. 


## Frontal Apache Nodejs

Arrêter le service:

```
systemctl stop apache2
```

Configurer le virtual host.

Exemple de configuration : 

```xml
<Proxy balancer://balancer-applitutoriel>
	
	BalancerMember http://127.0.0.1:8888 route=js1 retry=30 keepalive=on  ttl=10 loadfactor=3 flushpackets=auto timeout=300	
	BalancerMember http://127.0.0.1:8889 route=js2 retry=30 keepalive=on  ttl=10 loadfactor=3 flushpackets=auto timeout=300	
	BalancerMember http://127.0.0.1:8890 route=js3 retry=30 keepalive=on  ttl=10 loadfactor=3 flushpackets=auto timeout=300
	
	Require all granted
</Proxy>

AliasMatch ^/applitutoriel/static(.*)$ /var/www/applitutoriel/static$1 

ProxyPassMatch ^/applitutoriel/static !
ProxyPass /applitutoriel balancer://balancer-applitutoriel/applitutoriel stickysession=NODESESSIONID nofailover=on
```

Redémarrer le service:

```
systemctl start apache2
```



## Serveur NodeJS

Arrêter le service:

```
systemctl stop applitutoriel
```

Se positionner dans le répertoire d'installation de l'application :

Dézipper le livrable :

```
unzip applitutoriel-5.0.0.dynamic.zip /var/lib/nodejs/applitutoriel
```

Appliquer la configuration des fichiers : **/etc/nodejs/applitutoriel**

- environnement
- production.json
- production-1.json
- production-X.json

Redémarrer le service:

```
systemctl restart applitutoriel
```

## Frontal Apache Tomcat 8

Arrêter le service:

```
systemctl stop apache2
```

Configurer le virtual host.

Exemple de configuration : 

```xml
<Proxy balancer://balancer-applitutoriel-service>
	BalancerMember http://127.0.0.1:8009 route=jvmIDJVM-1 retry=30 keepalive=on ttl=10 loadfactor=3 flushpackets=auto timeout=300
	BalancerMember http://127.0.0.1:8010 route=jvmIDJVM-2 retry=30 keepalive=on ttl=10 loadfactor=3 flushpackets=auto timeout=300
	BalancerMember http://127.0.0.1:8011 route=jvmIDJVM-3 retry=30 keepalive=on ttl=10 loadfactor=3 flushpackets=auto timeout=300
	
	Require all granted	
</Proxy>

ProxyPass /applitutoriel-service balancer://balancer-applitutoriel/applitutoriel-service nofailover=on
```

Redémarrer le service:

```
systemctl start apache2
```

## Serveur Tomcat

Arrêter le service:

```
systemctl stop tomcat8
```

Déposer les fichiers de configurations à l’emplacement indiqué dans le fichier de paramétrage du contexte de l’application (variable **conf/applitutorielprop**).

```
unzip applitutoriel-service-5.0.0-config.zip /etc/tomcat8/appli/applitutoriel-service
```

Déposer le fichier de paramétrage du contexte de l’application dans le répertoire du serveur Tomcat situé sous : **/etc/tomcat8/Catalina/localhost/**

```xml
<Context useHttpOnly="true">
    <Environment name="conf/applitutorielprop" value="/etc/tomcat8/appli/applitutoriel-service" type="java.lang.String" />
</Context>
```

Déployer le war sur le serveur applicatif :

- **applitutoriel-service-5.0.0.war**

Renommer le fichier en **applitutoriel.war** avant de le déposer dans le répertoire tomcat 8 : **/var/lib/tomcat8/webapps**

Redémarrer le service

```
 systemctl start tomcat8
```

# Vérification

Ce chapitre décrit les actions à mener afin de vérifier que l’application applitutoriel a été correctement installée et configurée.


## Serveur Tomcat

Accès à l’application :

- Saisir l’url « **[protocole]://[url du serveur]:8080/applitutoriel-service/secteurs** » dans un navigateur web.

Le résultat doit ressembler à ceci :

```json
[
    {
        "auteurCreat": "user",
        "dateMajEnreg": 1331895600000,
        "auteurMajEnreg": "user",
        "dateSupprEnreg": null,
        "auteurSupprEnreg": null,
        "nom": "Habillement",
        "desc": "Secteur de l'habillement"
    },
    {
        "id": 4,
        "dateCreat": 1331809200000,
        "auteurCreat": "user",
        "dateMajEnreg": 1331895600000,
        "auteurMajEnreg": "user",
        "dateSupprEnreg": null,
        "auteurSupprEnreg": null,
        "nom": "Santé",
        "desc": "Secteur de la santé"
    },
    {
        "id": 5,
        "dateCreat": 1331550000000,
        "auteurCreat": "user",
        "dateMajEnreg": 1331636400000,
        "auteurMajEnreg": "user",
        "dateSupprEnreg": null,
        "auteurSupprEnreg": null,
        "nom": "Sport",
        "desc": "Secteur du sport"
    }
]
```

## Serveur NodeJS

Accès à l’application :

- Saisir l’url **[protocole]://[url du serveur node]/applitutoriel** dans un navigateur web et vérifier l'affichage suivant :

![Page d'acueil](./sources/application-tutoriel/accueil.png)

