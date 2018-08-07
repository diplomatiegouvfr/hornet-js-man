# Le composant TopButton

## Description

Le composant `TopButton` sert à revenir en haut de la page.
Il s'affiche lorsque le header de la page n'est plus visible.
Il s'affiche au dessus du footer.

![bouton](../sources/button/top-button.png)

Attributs du composant :

| Attibut   | Description                                                        | Obligatoire | Valeur par défaut | Type |
| --------- | ------------------------------------------------------------------ | --------- | ------------------- | -----|
| className | Classes css du bouton                                              | &nbsp;    | &nbsp;              | string |
| footer    | Identifiant de la balise du footer                                 | &nbsp;    | "footer-container"  | string |
| header    | Identifiant de la balise du header                                 | &nbsp;    | "header-container"  | string |
| id        | Identifiant unique du bouton                                       | &nbsp;    | &nbsp;              | string |
| name      | Nom du bouton                                                      | &nbsp;    | &nbsp;              | string |
| title     | Titre du bouton                                                    | &nbsp;    | &nbsp;              | string |


## Live coding

`Attention pour le live coding : garder l'identifiant "top-button-exemple" pour le TopButton. Celui-ci s'affichera avec une flèche rouge`

```javascript showroom
return(
    <TopButton id="top-button-exemple"/>
);
```