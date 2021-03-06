# VSCode configuration `Work in progress`

## Configuration typescript

doc vscode : `https://code.visualstudio.com/docs/languages/typescript#_using-newer-typescript-versions`


Afin d'être cohérent avec le builder Hornet.js, il est préférable de pointer vers la version typescript embarquée dans ses node_modules.

hornet-js-builder est installé en global.

Récupérer le prefix npm pour localiser l'installation de hornet-js-builder global.

``` sh
npm config ls -l | grep prefix
```

Exemple de résultat:

``` shell
prefix = "/usr"
```

L'installation globale doit etre : `/usr/lib/node_modules/`

Modifier les paramètres utilisateurs de vscode : ~/.config/Code/User/settings.json ou bien le raccourci : `Ctrl+,`

Exemple :
``` json
{
    "typescript.tsdk":  "/usr/lib/node_modules/hornet-js-builder/node_modules/typescript/lib"
}
```

### Installation custom typescript


Installer `typescript` en global.

``` shell
npm install -g typescript
```

Récupérer le prefix npm pour localiser l'installation de typescript global.

``` sh
npm config ls -l | grep prefix
```

Exemple de résultat:

``` shell
prefix = "/usr"
```

L'installation globale doit etre : `/usr/lib/node_modules/`

L'installation de typescript est alors : `/usr/lib/node_modules/typescript/lib`

Modifier les paramètres utilisateurs de vscode : ~/.config/Code/User/settings.json ou bien le raccourci : `Ctrl+,`

``` json
{
    "typescript.tsdk":  "/usr/lib/node_modules/typescript/lib"
}
```

## Masquer les fichiers js et js.map

doc vscode : https://code.visualstudio.com/docs/languages/typescript#_hiding-derived-javascript-files

Modifier les paramètres utilisateurs de vscode : ~/.config/Code/User/settings.json ou bien le raccourci : `Ctrl+,`

``` json
{
    "files.exclude": {
        "**/*.js": { "when": "$(basename).ts" },
        "**/**.js": { "when": "$(basename).tsx" },
        "**/*.js.map": true
    }
}
```

## Debugger dans vscode

Doc vscode : `https://code.visualstudio.com/docs/nodejs/nodejs-debugging`

Ouvrir le fichier launch.json, se positionner dans le tableau configuration puis utiliser `Ctrl + espace` pour faire apparaitre un menu de configuration.
Sélectionner : Node.js : Lancer un programme

``` json
{
    // Utilisez IntelliSense pour en savoir plus sur les attributs possibles.
    // Pointez pour afficher la description des attributs existants.
    // Pour plus d'informations, visitez : https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "node",
            "request": "launch",
            "name": "applitutoriel-js-lite-launch",
            "program": "${workspaceFolder}/applitutoriel-js-lite/index.ts",
            "outFiles": [
                "${workspaceFolder}/**/*.js"
            ],
            "cwd": "${workspaceFolder}/applitutoriel-js-lite",
            "skipFiles": [
                "${workspaceFolder}/node_modules/**/*.js"
            ]
        }
    ]
}
```

## Formattage de code (In progres)

Configuration possible de l'éditeur

Modifier les paramètres utilisateurs de vscode : ~/.config/Code/User/settings.json ou bien le raccourci : `Ctrl+,`
``` json
"editor.tabSize": 4,
"editor.insertSpaces": true,
"editor.renderWhitespace": "all",
```

## Configuration tslint 


### Pré-requis

- installer en global typescript et créer un lien symbolique de tsc
- installer le plugin TSLint dans vscode
- 

### Configuration

Insérer la configuration suivante dans le fichier `settings.json`:

```

    "extensions.autoUpdate": false,
    // Always show rule failures as warnings, independent of the tslint configuration.
    "tslint.alwaysShowRuleFailuresAsWarnings": true,
    // Always show the TSlint status bar item, not only when there are errors or warnings.
    "tslint.alwaysShowStatus": false,
    // Turns auto fix on save on or off, or defines which rules (e.g. `no-var-keyword`) to auto fix on save.
    "tslint.autoFixOnSave": true,
    // A path added to NODE_PATH when resolving the tslint module.
    "tslint.nodePath": "${pathNode}",
    "tslint.configFile": "${pathNode}/lib/node_modules/hornet-js-builder/src/conf/tslint.json",

```

penser à remplacer la variable `${pathNode}` afin de pointer vers le répertoire d'installation de node.js


## Activer watcher typescript et exécution automatique au démarrage de vscode

### Pré-requis

- installer en global typescript et créer un lien symbolique de tsc
- installer le plugin Blade Runner

### Configuration

Insérer la configuration suivante dans le fichier `tasks.json`:

```

{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "Watcher Typescript",
            "command": "tsc",
            "args": [
                "-p",
                "${path/workspace/}",
                "--pretty",
                "-w",
                "--sourceMap"
            ],
            "problemMatcher": [
                "$tsc-watch"
            ],
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "isBackground": true
        }
    ]
}

```

Remplacer `${path/workspace/}` par le chemin absolu vers le workspace
Le fait d'installer le plugin `Blade Runner` permettra de lancer la tâche au démarrage de l'application.