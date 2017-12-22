# Qualimétrie

Le builder Hornet.js permet de réaliser de la qualimétrie sur les projets. 

Pour lancer la qualimétrie sur un projet : 

```shell
hb lint
```

## Rapport 
 
Par défaut la qualimétrie s'affiche dans la console. 
Il est possible d'avoir plusieurs autres type de sortie. 

```shell
hb lint --lintReport json
```

Les valeurs possibles sont les suivantes, par défaut la valeur est à `prose` : 

- `json` prints stringified JSON to console.log.
- `prose` prints short human-readable failures to console.log.
- `verbose` prints longer human-readable failures to console.log.
- `full` is like verbose, but displays full path to the file
- `msbuild` for Visual Studio

Exemple de rapport "prose" : 

```shell
[09:14:03] [gulp-tslint] error index.ts[91, 40]: expected variable-declaration: 'modPath' to have a typedef
[09:14:03] [gulp-tslint] error index.ts[124, 41]: expected parameter: 'path' to have a typedef
[09:14:03] [gulp-tslint] error index.ts[136, 16]: expected variable-declaration: 'packageJson' to have a typedef
[09:14:03] [gulp-tslint] error index.ts[31, 1]: missing 'use strict'
...
```

Exemple de rapport `json` : 

```json
{
   "endPosition":{
      "character":16,
      "line":21,
      "position":620
   },
   "failure":"comment must start with a space",
   "name":"views/gen/gen-hom-page.tsx",
   "ruleName":"comment-format",
   "startPosition":{
      "character":14,
      "line":21,
      "position":618
   }
},
...
```

## Configuration tsLint.json

Par défaut, le builder propose sont propre fichier de règles contenu dans les sources : `src/conf/tslint.json`

Il est possible de surcharger celle-ci en passant le paramètre `lintRules`. Et ainsi de définir les règles spécifiques pour un projet.

```shell
hb lint --lintRules tslint.json
```

Dans ce cas précis, c'est le tslint.json où se lance la commande qui est pris en compte. Exemple, à la racine d'un projet. 

Il est également possible de définir un chemin absolu.

```shell
hb lint --lintRules c:\path\tslint.json
```
