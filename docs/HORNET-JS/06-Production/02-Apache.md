# Installation de Apache 

La configuration du frontal Apache passe par : 

- L'installation du serveur web Apache httpd
- L'installation et la configuration des modules
- La configuration des serveurs virtuels (optionnel)  

## Installation du packet Debian

Récupérer le paquet apache2 (2.4.10-9)

Lancer la commande d'installation

```shell
apt-get install apache2
```

## Utilisation du service SystemD

Fichier systemd encapsulé par Debian : **/run/systemd/generator.late/apache2.service**

```shell
systemctl stop apache2
systemctl start apache2
```

## Installation des modules

Les modules `apache` nécessaires sont les suivants :

```shell
a2enmod proxy
a2enmod proxy_http
a2enmod proxy_balancer
a2enmod headers
a2enmod rewrite
a2enmod lbmethod_byrequests
```

Vérifier dans la configuration d'Apache leur présence et leur activation : 

## Reconfiguration en mode worker

Le mode event est définit par défaut dans apache en version 2.4. 
Cependant, c'est le mode worker qui est préconisé. 

```shell
a2dismod mpm_event
a2enmod mpm_worker
systemctl restart apache2
```

## Configuration applicative apache nodejs

Créer un fichier dédié à l'application : 

```shell
touch	/etc/apache2/sites-enabled/MON_APPLI.conf
```

### Configuration des proxies

Pour chaque application / instance d'application desservi par le frontal Apache, il faut avoir les lignes de configuration du balancer et du ProxyPass.

Exemple concret : 

```xml
	<Proxy balancer://balancer-MON_APPLI>	
		BalancerMember http://127.0.0.1:8888 route=js1 retry=30 keepalive=on  ttl=10 loadfactor=3 flushpackets=auto timeout=300	
		BalancerMember http://127.0.0.1:8889 route=js2 retry=30 keepalive=on  ttl=10 loadfactor=3 flushpackets=auto timeout=300	
		BalancerMember http://127.0.0.1:8890 route=js3 retry=30 keepalive=on  ttl=10 loadfactor=3 flushpackets=auto timeout=300
		Require all granted
	</Proxy>
	AliasMatch ^/MON_APPLI/static([^/]+)/(.*)$ /var/www/MON_APPLI/static/$2
	ProxyPassMatch ^/MON_APPLI/static !
	ProxyPass /MON_APPLI balancer://balancer-MON_APPLI/MON_APPLI stickysession=NODESESSIONID nofailover=on
	ProxyErrorOverride On
	ErrorDocument 404 /var/www/html/MON_APPLI/static-LATEST_VERSION/404.html

```

La valeur de `route` doit être équivalente à celle présent dans le fichier production-INSTANCE.json : 

```json
	"server":{
		"route": "js1",
		...
	}

```

### Configuration du mode deflate

Modifier le module deflate corriger un bug apache sur la gestion du etag et des modules gzippés.

```shell
vim	/etc/apache2/mods-enabled/deflate.conf
```

```
	<IfModule mod_deflate.c>
		<IfModule mod_filter.c>.
			# PATH bug Apache https://bz.apache.org/bugzilla/show_bug.cgi?id=45023
			RequestHeader edit "If-None-Match" '^"((.*)-gzip)"$' '"$1", "$2"'
	
			# these are known to be safe with MSIE 6
			AddOutputFilterByType DEFLATE text/html text/plain text/xml
	
			# everything else may cause problems with MSIE 6
			AddOutputFilterByType DEFLATE text/css
			AddOutputFilterByType DEFLATE application/x-javascript application/javascript application/ecmascript
			AddOutputFilterByType DEFLATE application/rss+xml
			AddOutputFilterByType DEFLATE application/xml
		</IfModule>
	</IfModule>
```

## Configuration applicative apache tomcat

Créer un fichier dédié à l'application : 

```shell
touch	/etc/apache2/sites-enabled/MON_APPLI-service.conf
```

### Configuration des proxies pour la partie service

Pour chaque application / instance d'application desservi par le frontal Apache, il faut avoir les lignes de configuration du balancer et du ProxyPass.

Exemple concret : 

```xml

	<Proxy balancer://balancer-MON_APPLI-service>
		BalancerMember http://127.0.0.1:8080 route=jvmIDJVM-1 retry=30 keepalive=on ttl=10 loadfactor=3 flushpackets=auto timeout=300
		BalancerMember http://127.0.0.1:8081 route=jvmIDJVM-2 retry=30 keepalive=on ttl=10 loadfactor=3 flushpackets=auto timeout=300
		BalancerMember http://127.0.0.1:8082 route=jvmIDJVM-3 retry=30 keepalive=on ttl=10 loadfactor=3 flushpackets=auto timeout=300
		Require all granted	
	</Proxy>
	
	ProxyPass /MON_APPLI-service balancer://balancer-MON_APPLI/MON_APPLI-service nofailover=on

```

La valeur de `route` doit être équivalente à la valeur contenu dans le fichier server.xml de tomcat.


## Configuration d'un CDN (Optionnel)


L'architecture de Hornet permet d'utiliser un CDN pour la mise à disposition de thème.
Contrairement à la version 5.0.X, il n'est plus utilisé par défaut. Il est à utiliser que dans des cas bien précis sur le partage de ressources, ex : Logo.

Un thème peut contenir des polices de caractère (.woff, ...) qui ne peuvent plus être accessible directement depuis un autre domaine.
C'est pour cela qu'il est nécessaire de mettre en place le Access-Control-Allow-Origin sur la configuration apache.

La configuration doit contenir les lignes suivantes pour autoriser les requêtes cross-domain :

Globalement dans Apache :

```xml

	<IfModule mod_headers.c>
		Header set Access-Control-Allow-Origin *
	</IfModule>
	
```
ou bien directement dans la déclaration d'un VirtualHost :

```xml

	<FilesMatch ".(ttf|ttc|otf|eot|woff|font.css|css)">	
		Header set Access-Control-Allow-Origin *
	</FilesMatch>
	
```

Il est possible de configurer plus finement l'accès par sous-domaine, ex : 

```xml

	<FilesMatch ".(ttf|ttc|otf|eot|woff|font.css|css)">	
		#PROD SetEnvIf Origin "^(.*\.monsousdomaine\.fr)$" ORIGIN_SUB_DOMAIN=$1
		SetEnvIf Origin "^(.*)$" ORIGIN_SUB_DOMAIN=$1
	
		Header set Access-Control-Allow-Origin "%{ORIGIN_SUB_DOMAIN}e" env=ORIGIN_SUB_DOMAIN
	</FilesMatch>

```

Activer le module header de apache, puis redémarrer le serveur pour prise en compte.

```shell	
a2enmod headers
systemctl restart apache2	
```

Exemple de configuration complète : 

```xml

	<VirtualHost *:80>
		ServerAdmin webmaster@localhost
		DocumentRoot /var/www
		<Directory /var/www/>
			ExpiresActive On		
			Header append Cache-Control "public"
			Options FollowSymLinks MultiViews Indexes
			AllowOverride None
			Require all granted
		</Directory>
		ErrorLog ${APACHE_LOG_DIR}/error.log
		LogLevel warn
		CustomLog ${APACHE_LOG_DIR}/access.log combined
		<FilesMatch ".(ttf|ttc|otf|eot|woff|font.css|css)">	
			#PROD SetEnvIf Origin "^(.*\.monsousdomaine\.fr)$" ORIGIN_SUB_DOMAIN=$1
			SetEnvIf Origin "^(.*)$" ORIGIN_SUB_DOMAIN=$1
			Header set Access-Control-Allow-Origin "%{ORIGIN_SUB_DOMAIN}e" env=ORIGIN_SUB_DOMAIN
		</FilesMatch>
	</VirtualHost>

```
