# Personnalisation des thèmes :

## **Changer les couleurs :**
Principalement, se sont les couleurs qui seront à surcharger en langage scss.
Chaque couleur est définie par une **variable** et un **mixin** (fonction) scss. Par exemple, la couleur de base du site :

```sass
$baseColorLight: theme-color('global', 'white');
```

Le mixin (fonction) `theme-color($nomDuTheme, $couleur)` va récupérer les valeurs dans **abstract/_theming.scss** dans une liste sass composée de clés qui correspondent au nom du thème et de valeurs qui correspondent aux codes couleurs.
Ici la variable `$theme` contient les noms des thèmes (global, base, theme1...) ainsi que la paire clé : valeur des couleurs principales.

```sass
$themes: (
    'global': (
        'white': rgba(255, 255, 255, 1), //#fff
        'dark': rgba(0, 0, 0, 1), //#000
        'redError': rgba(230, 21, 29, 1), //#e6151d
        'redErrorlight': rgba(254, 216, 218, 1), //#fed8da
        'orangeWarning': rgba(255, 140, 0, 1), //darkorange
        'greenInfos': rgba(0, 128, 0, 1), //green
        'blueTitles': rgba(2, 64, 86, 1), //#024056
        'texts': rgba(35, 36, 37, 1), //#232425
        'boxShadows': rgba(0, 0, 0, 0.2)
    ),
    'base': (
        'primary': rgba(5, 121, 190, 1), //#0579be
        'secondary': rgba(3, 71, 110, 1), //#03476e
        'states': rgba(77, 144, 254, 1), //#4d90fe
        'texts': rgba(0, 123, 167, 1), // #007ba7
        'buttons': rgba(3, 71 , 110, 0.92),
        'pictos': rgba(0, 123, 167, 1) // #007ba7
    ),
    'theme1': (
        'primary': rgba(118, 155, 88, 1), // ~header - #769b58
        'secondary': rgba(67, 67, 77, 1), // ~pre-header - #43434d
        'states': rgba(225, 117 , 90, 1), // ~buttons hover, forms...
        'texts': rgba(224, 117, 85, 1), // ~breadcrumb / links - #e07555
        'buttons': rgba(225, 117 , 90, 1), // ~boutons - #e1755a
        'pictos': rgba(59, 116, 3, 1) // #3b7403
    ),
	...
);
```

Les variables de couleur qu'il est possible de surcharger sont celles-ci :

```sass
$baseColorLight: theme-color('global', 'white');
$baseColorDark: theme-color('global', 'dark');
$globalErrorColor: theme-color('global', 'redError');
$globalErrorBg: theme-color('global', 'redErrorlight');
$globalWarningColor: theme-color('global', 'orangeWarning');
$globalInfoColor: theme-color('global', 'greenInfos');
$globalSiteBg: #f3f3f3;
$globalTitleColor: theme-color('global', 'blueTitles');
$globalSubTitle: theme-color($variant: 'primary');
$globalLinkColor: theme-color($variant: 'primary');
$globalPageSiteColor: theme-color('global', 'texts');
$buttonBoxShadow: theme-color('global', 'boxShadows');
$calendarBaseColor: theme-color('global', 'white');
$calendarPrimaryColor: theme-color($variant: 'primary');
$calendarSecondaryColor: #666;
$formBgGlobal: #f4f4f4;
$tableBaseColor: theme-color('global', 'white');
$tableHeaderColor: theme-color($variant: 'primary');
```

**Nb : chaque variable de couleur peut être changée via le mixin theme-color($nomTheme, $couleur) si la valeur est présente dans _theming.scss. Sinon un code couleur suffit (hexa, rgba, hsl...).**

## Surcharger les styles :
Pour surcharger les styles, il suffit de cibler une classe précise et de la modifier dans le fichier de surcharge (n'importe quel dossier sass dans `applitutoriel-js` ou `applitutoriel-js-lite`).

*Exemple pour changer le background du header et le outline des ses liens :*

```sass
$headerBg: red;
$headerSubLinkHover: blue;
/// header
#header {
    background: $headerBg none repeat scroll 0 0;

    .header-link {
        &:hover,
        &:focus {
            outline: 1px solid $headerSubLinkHover;
        }
    }
}
```

**Nb : il est possible d'avoir besoin d'ajouter `!important` sur certaines propriétés selon l'héritage.**