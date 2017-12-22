# Le composant TopButton

## Description

Le composant `TopButton` sert à revenir en haut de la page.
Il s'affiche lorsque le header de la page n'est plus visible.
Il s'affiche au dessus du footer.

![bouton](../sources/button/top-button.png)

Attributs du composant :

| attibut   | description                                                        | valeur par defaut |
| --------- | ------------------------------------------------------------------ | --------- |
| className | Classes css du bouton| &nbsp;  |
| footer    | Identifiant de la balise du footer | "ft" |
| header    | Identifiant de la balise du header |  "header-container" |
| id        | Identifiant unique du bouton | &nbsp; |
| name      | Nom du bouton | &nbsp; |
| title     | Titre du bouton | &nbsp;  |

exemple:

`Attention pour le live coding : garder l'identifiant "top-button-exemple" pour le TopButton. Celui-ci s'affichera avec une flèche rouge`

```javascript showroom
return(
    <main id="page">
        <TopButton id="top-button-exemple"/>
    </main>
);
```