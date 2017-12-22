# Installation Tomcat 8

L'installation d'une instance Tomcat passe par : 

- L'installation du paquet Debian openjdk8
- L'installation du paquet Debian tomcat8 mono et multi-instance
- La configuration du lanceur SystemD

## Installation openjdk8

Ajout le miroir backport "jessie-backports" sur Debian afin de récupérer le paquet openjdk-8-jdk.

Lancer la commande d'installation :

```shell
apt-get install -t jessie-backports ca-certificates-java openjdk-8-jdk
```

Vérifier l'installation avec la commande :

```shell
java -version
```

```shell
openjdk version "1.8.0_40-internal"
OpenJDK Runtime Environment (build 1.8.0_40-internal-b22)
OpenJDK 64-Bit Server VM (build 25.40-b25, mixed mode)
```

Si le jdk installé n’est pas dans la bonne version : 

```shell
update-java-alternatives -l
update-java-alternatives -s java-1.8.0-openjdk-amd64
```

Faire un remove de la version courante : 

```shell
apt-get remove lejdkinstalle
```

## Installation mono-instance


### Installation du package

Installer le paquet tomcat8 (8.0.14-1).

```shell
apt-get install tomcat8
```

Créer le répertoire : 

```shell
mkdir  /etc/tomcat8/appli
```

Mettre les droits sur ce répertoire :

```shell
chmod 755 /etc/tomcat8/appli
```

### Configuration "Default"

Editer le fichier : `/etc/default/tomcat8`

Modifier la valeur de la variable : `JAVA_HOME`

```
JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-amd64
```

Modifier la valeur de la variable : `JAVA_OPTS`

```
JAVA_OPTS="-Djava.awt.headless=true -Xmx1024M -Xms1024M -XX:+PrintGCDetails -XX:+PrintGC -Xloggc:/var/log/tomcat8/loggc.log -Duser.language=fr -Duser.region=FR -Dfile.encoding=utf-8 -Duser.timezone=Europe/Paris"`
```

Modifier le chemin temporaire : `JVM_TMP`

```
JVM_TMP=/var/tmp/tomcat8
```

###	Configuration server.conf 

Attention : 
Ce fichier de configuration en mode multi-instance est déjà pré-renseigné et ne nécessite normalement aucune modification

Editer le fichier : `/etc/tomcat8-INSTANCE/server.xml`

Mettre les valeurs false aux clés suivantes :

```
unpackWARs="false"
autoDeploy="false"
```

Ajouter la jvmRoute à la ligne de configuration suivante : 

```xml
<Engine name="Catalina" defaultHost="localhost" jvmRoute="jvmIDJVM-1">
```

IDJVM doit correspondre à la variable d’environnement $HOSTNAME

Vérifier que la configuration suivante est bien active : 

```xml
<Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="localhost_access_log" suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />
```

## Installation multi-instance

Installer en pre-requis, le package tomcat8. 

```shell
apt-get install tomcat8
```

Récupérer puis installer le package : `tomcat8-instance-8.0.28.deb`

```shell
dpkg –i tomcat8-instance-8.0.28.deb
```

Créer les instances tomcat :

```shell
tomcat8-instance-create -p 8081 -c 8006 tomcat8-1
tomcat8-instance-create -p 8082 -c 8007 tomcat8-2
tomcat8-instance-create -p 8083 -c 8008 tomcat8-3
```

## Utilisation du service SystemD

Fichier systemd encapsulé par Debian : `/run/systemd/generator.late/tomcat8.service`

```shell
systemctl stop tomcat8
systemctl start tomcat8
```

