# Le composant 'routeur' du module hornet-js-core

## Présentation
Ce composant est central dans l'application car il gère toutes les actions à exécuter lors d'un changement de route (= changement d'url).

Le routeur de Hornet-js est basé sur le composant isomorphe '*director*' (https://github.com/flatiron/director) qu'il vient surcharger afin de proposer un cadre de développement unifié

## Principe de fonctionnement
Lors du chargement de l'application, le routeur est instancié avec des routes de bases ainsi que des routes à chargement différés (routes en mode lazy).

A chacune de ces routes correspond une fonction qui est exécutée automatiquement par le routeur lorsque celle-ci est activée.

Cette fonction doit retourner un objet ([voir ci-dessous](#instanciation-des-routes)) qui va permettre au routeur de résoudre les actions à effectuer sur cette route et donc de rendre correctement la page.

## Instanciation des routes en mode non lazy

Pour configurer les routes, chaque application doit fournir une classe de base implémentant l'interface *IRoutesBuilder* présente dans le module **router.ts**.

Cette classe comporte une méthode `build` qui prend en argument une fonction de '*matching*' qui est automatiquement injectée par le routeur (voir documentation [core-serveur](core-server.md) et [core-client](core-client.md) pour la configuration):

```javascript
class Routes implements routerInterfaces.IRoutesBuilder {
    buildViewRoutes(match:routerInterfaces.MatchFn) {
        match('/', () => {
            return {
                composant: require('src/views/gen/gen-hom-page')
            }
        });
        match('/contact', () => {
           return {
               composant: contactPage
           }
       });
    }
    buildDataRoutes(match:routerInterfaces.MatchFn) {
        match('/envoyer', (context) => {
          var formData = context.req.body;
          var actionValider = new fvaNS.FormValidationAction().withApplicationForm(<fvaNS.NewsFormValidation> ContactForm({
              data: formData
          }, context.actionContext.i18n('contactPage.form'))).dispatchIfFormNotValid('CONTACT_RECEIVE_FORM_DATA');
          return {
              actions: [actionValider, new actionsContact.Send().withPayload(formData)],
              roles:['AppliTUTO_Admin']
          };
        }, "post");
    }
}
```

Cette fonction de matching prend les arguments suivants:

| Paramètre | Description |
|------- | ----------- |
| path | L'url de la route. Peut-être une regex. |
| handler | La méthode qui est appelée par le routeur lorsque la route est activée |
| method | La méthode http sur laquelle la route doit s'activer: Valeurs possibles: **get**, **post** ou **both** (pour indiquer que la route s'active sur le get et le post) |
[Routeur - matching - arguments]

Lorsqu'une route est activée, le routeur appelle automatiquement la fonction '*handler*' avec les arguments suivants:

| Paramètre | Description |
|------- | ----------- |
| context | Le contexte courant permettant d'accéder à divers éléments de la requête. [Voir ci-dessous](#le-contexte-de-la-requete) |
| param1 | Optionnel. Le premier paramètre sur une route de type: /url/:param1 |
| param2 | Optionnel. Le second paramètre sur une route de type: /url/:param1/:param2 |
| paramX | Optionnel. Le Xeme paramètre sur une route de type: /url/:param1/.../:paramX |
[Routeur - handler - arguments]

Cette fonction doit préparer les actions que le routeur exécutera ensuite afin de rendre correctement la page demandée. Elle retourne un objet avec les champs suivants:

| Paramètre | Description |
|------- | ----------- |
| actions | Un tableau des actions à chainer avant de rendre cette route |
| composant | Le composant React à rendre sur cette page |
| roles | Optionnel. Un tableau contenant des noms des rôles à avoir pour accéder à cette page.L'utilisateur doit avoir au moins un rôle du tableau pour que cette route lui soit accessible. |
[Routeur - actions]

### Le contexte de la requete
Cet objet permet d'accéder aux informations courantes de la requête:

| Attribut | Description |
|------- | ----------- |
| actionContext | L'ActionContext de Fluxible |
| req | Les éléments de la requête|
|res|Les informations de la réponse|
[Routeur - requête]

L'objet *req* contient notamment les propriétés suivantes:

| Attribut | Description |
|------- | ----------- |
| body | Le corps de la requête sous la forme d'un objet javascript |
| query | Les paramètres de la requête sous la forme d'un objet javascript.<br /> Exemple: page?param1=XX&param2=YY => {param1:XX, param2:YY} |
|session| La session courante de l'utilisateur|
[Routeur - requête - propriétés]

### Notes sur les actions et les promises

Les actions sont chainées grâces à des promises.
Ceci a les implications suivantes:

- Si une action échoue, les promises suivantes ne sont pas exécutées
- Chaque action reçoit en paramètre le résultat de la promise précédente.

Exemple:

```javascript
export class EnregisterDansStore extends Action {
    execute(resolve, reject) {
        this.actionContext.dispatch('XXXXX', dataPromisePrecedente);
        resolve('nextData');
    }
}
```

- Le routeur enregistre de lui-même une promise recevant le résultat des promises résolues en erreur. L'erreur est alors enregistrée dans le `NotiticationStore`

## Instanciation des routes en mode lazy

La fonction de matching propose aussi une méthode `lazy` qui prend les arguments suivants:

| Paramètre | Description |
|------- | ----------- |
| path | L'url parente du groupe de routes. Toutes les routes à charger auront comme base ce path. <br />Note: En interne le routeur génère une route qui match: `path\.*` |
| fileToLoad | Path (sous la forme d'un lien textuel) vers la classe de routes à charger. <br /> Voir la documentation de la fonction *routesLoaderfn* pour plus d'informations: [core-serveur](core-server.md) et [core-client](core-client.md)|
[Routeur - routes lazy]

### Mode lazy côté serveur

Le mode `lazy` n'ayant pas d'intéret pour le serveur, celui-ci va charger toutes les routes en mémoire dès le chargement du routeur.

### Mode lazy côté client

Dès qu'une route `lazy` est détectée côté client, les actions suivantes sont effectuées par le routeur:

- Chargement de la classe de routes correspondante
- Suppression de la route créée automatiquement au chargement et qui s'applique sur tous les paths fils (`path\.*`).
- Insertion des nouvelles routes dans le routeur en remplacement de la route supprimée juste avant
- Relance d'un cycle de résolution de route.

## Ajout de filtres sur des patterns d'url

Le routeur offre la possibilité à l'application de definir des filtres sur des pattern d'url.

Pour definir un filtre ajouter dans le fichier `server.ts` de l'application une variable à l'objet de configuration du routeur.

```javascript
    function filterAllCallback(req:express.Request, res:express.Response, next:any){
        logger.debug("filterAllCallback");
        next();
    }
    var filters = [{pattern:"/*", callback: filterAllCallback }];
    var configServer:serverNs.ServerConfiguration = {
        ...
        filters:filters
    };
```

## Notes importantes côté client

Voici des points importants qui s'appliquent côté client:

- Le routeur est configuré en mode **html5 history**
- Un *eventListener* est automatiquement enregistré sur :
   - tous les évenements de type '**click**'
   - et portant sur un élement html de type '**a**'
   - dont la propriété '**href**' est valorisée et ne commence pas par '**#**'
   - et dont la propriété '**data-pass-thru**' n'est pas valorisée <br />
Pour ces évènements l'action par défaut est annulée et le contenu de la propriété '**href**' est passé au routeur pour activer la nouvelle route.
Cet évènement permet ainsi d'avoir un fonctionnement SPA sans avoir à gérer explicitement l'évènement du click sur un lien.
   - le routeur est accessible dans l'objet **window** (*ce comportement est susceptible d'être modifié dans les versions supérieures de hornet 5.0.0*)

```javascript
    window.routeur;
```

 - le routeur expose la méthode '**setRouteInternal**' qui permet de simuler un changement de route sans changer l'url dans la barre d'adresse du navigateur.Elle est utilisée pour les cas de POST qui ne doivent pas modifier l'adresse.

```javascript
    window.routeur.setRouteInternal('/component');
```
