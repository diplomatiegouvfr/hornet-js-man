# Thèmes - `Deprecated`

Ce document précise les étapes de déploiement des livrables hornet-themes sur un CDN.

## Environnement cible : CDN

Si les thêmes peuvent être maintenant embarqués dans les applications Hornet.js, ils peuvent toujours être déployés sur un CDN pour des applications fonctionnant sur ce principe. 

# Livrables

Le packaging des thèmes est fourni sous la forme d'un zip qui est déployé derrière un frontal WEB (Apache) : `hornet-themes-*.zip`

# Déploiement

Ce chapitre décrit les différentes étapes nécessaires au déploiement des thèmes dans le CDN.

## Pré-requis

Le Frontal Apache doit être configuré avec un `DocumentRoot`, ou un alias permettant d'accéder au répertoire qui contiendra les thèmes.

Par défaut sous debian : `/var/www`

La configuration du Apache doit permettre les `Cross-Domains` afin de pouvoir charger les polices d'écritures.

Pour celà, activer le module header de apache et mettre en place la configuration suivantes :

```shell
a2enmod headers
```

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

## Dépot des thèmes

1. Récupérer le zip contenant le thème.
2. Le décompresser dans le répertoire cible. /hornet

L'arboressence obtenue doit ressembler à ceci :

```
[DocumentRoot ou Alias]
	/hornet
		/5.1.X
			/NOM_THEME
```

## Vérification

Accès aux ressources du CDN :

- Saisir l’url ` [PROTOCOLE]://[URL]:[PORT]/hornet/5.1.X/NOM_THEME/css/theme.css` dans un navigateur web.
