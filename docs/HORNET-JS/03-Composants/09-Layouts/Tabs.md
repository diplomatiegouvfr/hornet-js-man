# Les composants de Tabs

## Description

Tous les éléments d’onglet `Tabs` et  `Tab` sont chargés dans la page, l’affichage sous forme d’onglet est ensuite réalisé si le JavaScript est activé. Un composant d’onglet consiste en une liste de liens qui ciblent des éléments de contenu.

![Image de deux onglets](../sources/tab/onglets.png)

## Utilisation

Les balises de type `Tab` sont englobées par une balise de type `Tabs`,
Les balises `TabHeader` et `TabContent` sont engloblées par une balise de type `Tabs` et sont optionnelles,
Un `Tab` ne peut avoir qu'un seul TabHearder et 1 seul TabContent:

```javascript
<Tabs>
    <Tab>
    </Tab>
    <Tab>
    </Tab>
    <Tab>
        <TabHeader>
        </TabHeader>
        <TabContent>
        </TabContent>
    </Tab>
    ...
</Tabs>
```

Les attributs du composant `Tabs` sont les suivants:

| Attribut         | Description                                         | Obligatoire |Valeur par défaut                    | Type |
| ---------------- | --------------------------------------------------- | ------------| ------------------------------------|------|
| id               | Identifiant de l'onglet                             | Oui | |string |
| panelId          | Préfixe des id's de chaque div contenant l'onglet   || "panel"                                          |string|
| selectedTabIndex | Index de l'onglet à afficher                        || 0                                                |number|
| dataSource       | Données passée au tabs                              |||DataSource<any>|
| beforeHideTab    | Méthode appelée avant le masquage d'un onglet       ||                                                  |(tabRef?: Tab, index?: number) => void|
| afterShowTab     | Méthode appelée après l'affichage d'un onglet       ||                                                  |(tabRef?: Tab, index?: number) => void|
| addTabFunction   | Méthode appelée lors d'un click sur le bouton d'ajout d'onglet ||                                       |void/Function|
| addButtonTtitle  | Title du buton d'ajout d'onglet                     ||                                                  |string|
| deleteTabFunction| Méthode appelée lors d'un click sur le bouton de suppression d'un onglet ||                             |void/Function|
| deleteButtonTitle| Title du buton de suppression d'un onglet           ||                                                  |string|

Les attributs du composant `Tab` sont les suivants:

| Attribut         | Description                                                     | Obligatoire | valeur par défaut                                |Type|
| ---------------- | --------------------------------------------------------------- |  -----------| ------------------------------------------------ |----|
| forceRender      | Définit si l'onglet sera rendu de force ou non (depreceated)    ||false                                                  |boolean|
| id               | Identifiant du tab                                              ||"tab"|string|
| index            | Index du tab                                                    |||number|
| isVisible        | Définit si l'onglet est visible ou non                          ||                                                  |boolean|
| mount            | Définit la méthode de chargement de l'onglet                    || true                                             |boolean|
| onSelect         | Méthode appelée lorsque l'onglet est sélectionné ou sélectionné ||                                                  |Function|
| panelId          | Id du contenu de l'onglet                                       ||                                             |string|
| prefixId         | Préfixe de l'identifiant de la section                          ||                                                  |string|
| title            | Titre de l'onglet                                               ||                                                  |string|
| isDeletable      | Définit si l'onglet est supprimable                             ||                                                  |boolean|
| deleteButtonTitle| Titre du bouton de suppression de l'onglet                      ||                                                  |string|
| deleteTabFunction|Méthode appelée lors d'un click sur le bouton de suppressiond de onglet (sera appelée en priorité sur la méthode définit dans tab)|  | |void/Function|

Les attributs du composant `TabHeader` sont les suivants:
Elle permet de surcharger le titre de l'onglet.

| Attribut         | Description                                                   | Obligatoire | Valeur par défaut                                |Type|
| ---------------- | ------------------------------------------------------------- | ------------| ------------------------------------------------ |----|
| aucun            | ------------------------------------------------------------- | ------------| ------------------------------------------------ |----|
| ---------------- | ------------------------------------------------------------- | ------------| ------------------------------------------------ |----|

Les attributs du composant `TabContent` sont les suivants:
Permet de lier l'état (in progress/idle) d'un datasource à celui de l'onglet (intéressant pour synchroniser le loader de l'onglet automatiquement).

| Attribut              | Description                                                   | Obligatoire | Valeur par défaut                                | Type |
| --------------------- | ------------------------------------------------------------- | ------------| ------------------------------------------------ |------|
| dataSource            | DataSource                                                    | |none (optionnel)                                 |DataSource<any> |


Les méthodes du composant `Tabs` sont les suivants:

| méthodes                | description                                                                                            |  Paramètres                                      |
| ----------------------- | ------------------------------------------------------------------------------------------------------ | ------------------------------------------------ |
| addElements             | Permet d'ajouter des elements Tab                                                                      | position: number, ...tabsToAdd: JSX.Element[]    |
| removeElementsById      | Permet de supprimer des Tab en fonction des Id                                                         | ids: number[]                                    |
| removeElementsByIdWithCb| Permet de supprimer des Tab en fonction des Id avec un callback                                        | ids: number[], cb: Function                      |
| removeElementsByIndex   | Permet de supprimer des Tab en fonction des index                                                      | indexes: number[]                                |
| getCurrentIndexSelected | Renvoie l'index du tab courrant                                                                        | none                                             |
| refresh                 | Force le rafraîchissement du tabs (uniquement lorsqu'on ajoute/supprime un composant dans un tab)      | none                                             |
| getTabsNumber           | Renvoie le nombre d'onglet                                                                             | none                                             |
| getTabPosition          | Renvoie la position de l'onglet dans la liste d'onglet                                                 | index: number                                    |
| getIndexAt              | Retourne l'index de l'onglet a la position indiquée                                                    | position: number                                 |



Dans notre exemple, le formulaire est inclus dans le premier des deux onglets.

```javascript

<Tabs id="exemple-tabs" selectedTabIndex={0}>
    <Tab title="Identité" >
        <Form ...>
            ...
        </Form>
   	</Tab>
    <Tab titre="Produits">
        <h5>Liste des produits</h5>
    </Tab>
</Tabs>
```

## Live coding

```javascript showroom

return (
    <Tabs id="tabsExemple" selectedTabIndex={0}>
        <Tab title={"Onglet 1"}>
        </Tab>
        <Tab title={"Onglet 2"}>
        </Tab>
    </Tabs>
 );
```

```javascript

<Tabs id="exemple-tabs" selectedTabIndex={0}>
    <Tab title="Identité" >
        <Form ...>
            ...
        </Form>
   	</Tab>
    <Tab titre="Produits" mount={false}>
        <h5>Liste des produits</h5>
    </Tab>
</Tabs>
```
