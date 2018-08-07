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