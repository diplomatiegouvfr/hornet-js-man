# Le composant SessionNotification

## Description

Le composant permet d'afficher des messages à afficher à l'utilisateur indiquant la fin imminente de la session :

## Utilisation

Le composant SessionNotification sert à afficher les messages avertissant l'utilisateur d'expiration de session.
Ce composant react écoute les evènements lancés par l'api service du socle, il y a 3 évènements:
     
| Nom                                          | Description                                                         |
| ---------------------------------------------| --------------------------------------------------------------------|
| SESSION_WILL_EXPIRE_START_NOTIFICATION_EVENT | C'est le premier évènement qui est lancé lorque le décompte commence|
| SESSION_WILL_EXPIRE_NOTIFICATION_EVENT       | Ces evènements sont lancés toutes les intervalles ( cf default.json "server.notifications.notifSessionTimeoutRepeat)|
| SESSION_REFRESHED_NOTIFICATION_EVENT         | Cet évenement indique que le décompte est remis au début par l'api service|

Ce composant n'a pas de rendu mais il est possible de de surcharger la methode render du composant react.

## Création d'une notification


Il existe deux types des composants pour la gestion des notifications de fin de session du serveur NodeJs

### Composant classique

Ce composant va émettre des notifications Navigateur

voici un exemple d'utilisation dans hornet-app
Les valeurs
AppSharedProps.set("sessionTimeout", Utils.config.get("server.sessionTimeout"));
AppSharedProps.set("notifSessionTimeout", Utils.config.get("server.notifications.sessionTimeoutDelay"));

```javascript
            <div id="site">
                <HeaderPage scrollHeight={35}>
                .....
                </HeaderPage>
                <HornetContent ... />
                <SessionExpireNotification />
                <FooterPage ... >
                ....
                </FooterPage>

```

### Composant NotificationSessionFooter

Ce composant s'affiche en bas, à droite de la page:

![Notification fin de session NodeJs](../sources/notification/notification-session.png)

```javascript
            <div id="site">
                <HeaderPage scrollHeight={35}>
                .....
                </HeaderPage>
                <HornetContent ... />
                <NotificationSessionFooter />
                <FooterPage ... >
                ....
                </FooterPage>

```

Ce composant indique qu'une déconnexion est imminente. Si aucune action utilisateur n'est faite,
le composant se réduit au bout de 4 secondes pour ensuite de nouveau s'agrandir dès qu'un évènement `SESSION_WILL_EXPIRE_NOTIFICATION_EVENT` est émis.
Il met également à disposition un lien permettant de se reconnecter.