# Mailer

## Le composant Mailer

Le composant `Mailer` permet l'intéraction avec un serveur de mail pour réaliser des envois de mail.

Il est basé sur la library NodeMailer.

## Exemple d'utilisation

### Préparer un mail avec `NodeMailerMessage`

```javascript
    let templatedMessage: string = new Template(HornetComponent.getI18n("contactPage.mailTemplate")).process(valuesToWriteIntoMessage, "?");
    let mailToSend: NodeMailerMessage = {
        from: Utils.config.getOrDefault("mail.mailSender", undefined),
        to: Utils.config.getOrDefault("mail.mailReceiver", undefined), //récupérer mail dans config
        subject: HornetComponent.getI18n("contactPage.subject"),
        text: templatedMessage
    };
```

### Envoyer un mail avec `Mailer`

```javascript
    Mailer.sendMail(mailToSend, Utils.config.getOrDefault("mail.config", undefined));
```