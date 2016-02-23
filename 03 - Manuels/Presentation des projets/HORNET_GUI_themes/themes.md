
# Hornet-themes

Hornet-themes est le projet qui permet de construire les thèmes du framework Hornet. 
Pour cela, il met à disposition un thème par défaut : `default`

Il est basé sur le framework `purecss`.

## Pré-requis

- `nodejs` 4.X
- `hornet-js-builder` 1.X installé en global

## Installation

- Récupérer le projet `hornet-themes`

```shell
$ hb install
```

## Exécution 

```shell
$ hb start
```

## Vérification

Accès aux ressources du CDN via un navigateur web : 

`[PROTOCOLE]://[URL]:[PORT]/hornet/5.0.X/default/css/theme.css` 

Par défaut : 

`http://localhost:7777/5.0.X/default/css/theme.css`

Le fichier ainsi affiché doit commencer par ceci :

```css
/*!
Pure v0.5.0
Copyright 2014 Yahoo! Inc. All rights reserved.
Licensed under the BSD License.
https://github.com/yui/pure/blob/master/LICENSE.md
*//*!
```

## Packaging

```shell
$ hb package
```

Le livrable est disponible dans le répertoire :

`target\hornet-themes-default-5.0.X.zip`

## Déploiement sur Apache

### Pré-requis

Le Frontal Apache doit être configuré avec un `DocumentRoot`, ou un alias permettant d'accéder au répertoire qui contiendra les thèmes.

La configuration du Apache doit permettre les `Cross-Domains` afin de pouvoir charger les polices d'écritures.

Pour celà la configuration doit comporter les lignes suivantes :

```xml
	<IfModule mod_headers.c>
		Header set Access-Control-Allow-Origin *
	</IfModule>
```

### Déploiement

1. Récupérer le zip contenant les thèmes.
2. Le décompresser dans le répertoire cible.

L'arborescence obtenue doit ressembler à ceci :

```
[DocumentRoot ou Alias]
	/hornet
		/5.0.X
			/default
```