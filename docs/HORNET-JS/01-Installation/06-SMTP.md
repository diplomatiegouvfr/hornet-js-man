# SMTP poste dev #

Paramétrage d'un serveur SMTP sur un Debian 9.

## Installation de l'apache2

```
sudo apt-get install apache2
```

## Installation de php

```
sudo apt-get install php5
```

## Installation de postfix (SMTP)

```
sudo apt-get install postfix
```

Choix 1 : Internet Site

Choix 2 : Nom de domaine => Nom de la machine

## Installation de SASL (SMTP Authentication)

```
sudo apt-get install libsasl2-2 libsasl2-modules sasl2-bin
```

## Configuration de SASL

```
sudo vi /etc/default/saslauthd
```

```
- START=no
+ START=yes

- OPTIONS="-c -m /var/run/saslauthd"
+ OPTIONS="-c -r -m /var/spool/postfix/var/run/saslauthd"
```

```
sudo mkdir -p /var/spool/postfix/var/run/saslauthd

sudo rm -rf /var/run/saslauthd

sudo ln -s /var/spool/postfix/var/run/saslauthd /var/run/saslauthd

sudo vi /etc/postfix/sasl/smtpd.conf
```

```
+ pwcheck_method: saslauthd
+ mech_list: plain login
+ allow_plaintext: true
```

```
sudo adduser postfix sasl

sudo chgrp postfix /etc/postfix/sasl/smtpd.conf

sudo chmod 640 /etc/postfix/sasl/smtpd.conf

sudo /etc/init.d/postfix restart

sudo /etc/init.d/saslauthd restart
```

### Création de l'utilisateur

```
sudo saslpasswd2 applituto
=>MDP: applituto
```

### Passage en sasldb

Modification du daemon pour etre en sasldb

```
vi /etc/default/saslauthd
```

```
- MECHANISMS="pam"
+ MECHANISMS="sasldb"
```

```
sudo service saslauthd restart
```

### Test

```
sudo testsaslauthd -s sasldb -u applituto -p applituto
```

## configuration de postfix

```
sudo vi /etc/postfix/main.cf
```

```
# See /usr/share/postfix/main.cf.dist for a commented, more complete version


# Debian specific:  Specifying a file name will cause the first
# line of that file to be used as the name.  The Debian default
# is /etc/mailname.
#myorigin = /etc/mailname

smtpd_banner = $myhostname ESMTP $mail_name (Debian/GNU)
biff = no

# appending .domain is the MUA's job.
append_dot_mydomain = no

# Uncomment the next line to generate "delayed mail" warnings
#delay_warning_time = 4h

readme_directory = no

# TLS parameters
smtpd_tls_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
smtpd_tls_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
smtpd_use_tls=yes
#smtpd_use_tls=no
smtpd_tls_session_cache_database = btree:${data_directory}/smtpd_scache
smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache

# See /usr/share/doc/postfix/TLS_README.gz in the postfix-doc package for
# information on enabling SSL in the smtp client.

smtpd_relay_restrictions = permit_mynetworks permit_sasl_authenticated defer_unauth_destination
myhostname = workstation13.intern
alias_maps = hash:/etc/aliases
alias_database = hash:/etc/aliases
myorigin = /etc/mailname
mydestination = workstation13.devng.diplomatie.gouv.fr, workstation13.intern, localhost.intern, localhost
relayhost =
mynetworks = 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128
mailbox_size_limit = 0
recipient_delimiter = +
inet_interfaces = all

#### MAE Specific

smtpd_sasl_auth_enable = yes
smtpd_sasl_security_options = noanonymous
smtpd_sasl_authenticated_header = yes
canonical_maps = regexp:/etc/postfix/canonical-redirect
```

```
sudo vi /etc/postfix/canonical-redirect
```

```
+ /^.*$/ debianUserg@workstationXX.intern
```

```
sudo postmap /etc/postfix/canonical-redirect

sudo usermod -a -G mail "votre login"

sudo postfix reload
```

## installation de dovecot (IMAP)

```
sudo apt-get install dovecot-imapd
```

## installation de squirremail (Webmail)

```
sudo apt-get install squirrelmail

sudo ln -s /etc/squirrelmail/apache.conf /etc/apache2/sites-available/squirrelmail.conf
```

### Activation du site

```
sudo a2ensite squirrelmail
sudo apache2ctl restart
```

## Sources

http://gogs.info/books/debian-mail/chunked/postfix.sasl.html
http://www.proulx.com/~bob/doc/HOWTO-Postfix-SASL.html
http://www.postfix.org/SASL_README.html#auxprop_sasldb
https://stackoverflow.com/questions/38431592/sending-mail-in-node-js-using-nodemailer
http://www.checkmarkmedia.com/blog/redirect-all-emails-to-one-address/


