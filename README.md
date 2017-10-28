# Hadoop-v2.7.4-Installation

## Présentation

Cette partie de projet s'intéresse à l'installation et la configuration d'un cluster Hadoop simple noeud en utilisant la version proposée par la fondation Apache. Nous décrirons comment réaliser une telle installation dans un système virtualisé Linux (Ubuntu 16.04). Bien entendu, Hadoop n'a d'intérêt que s'il est utilisé dans un cluster composé de plusieurs machines. En effet, utiliser Hadoop dans un environnement simple noeud, comme nous allons le faire, n'a de sens que pour tester la configuration de l'installation ou fournir un environnement de développement MapReduce.

Pour télécharger Hadoop deux solutions sont disponibles. La première solution est d'utiliser la version proposée par la fondation Apache. Cette version est celle de référence et contient le noyau et quelques interfaces d'administration très simplifiée. La seconde solution est d'utiliser les distributions fournies par des entreprises qui font du service autour de Hadoop. Comme il y a une valeur ajoutée non négligeable, ces distributions fournissent des services payants. Sans être exhaustif, voici trois sociétés qui font de Hadoop leur spécialité.

* Cloudera - https://www.cloudera.com/
* Hortonworks - https://hortonworks.com/
* MapR Technologies - https://mapr.com/

Dans le cadre de ce cours, nous utiliserons la distribution Hadoop fournie par Apache. Par ailleurs, si nous consultons le site de la fondation Apache, on se retrouve avec quatre versions majeures Hadoop. À l'écriture de ce cours, il s'agissait des versions 0.23.X, 1.2.X, 2.2.X et 2.X.X. Les trois premières versions correspondent à des versions stables et aptes à passer en production. La dernière est la version en cours.

## Environnement de travail

L'installation de Hadoop 2 dans ce cours sera réalisée dans un environnement Linux virtualisé (Ubuntu 16.04 LTS).

Il est préférable d’utiliser VMware 10 :

* https://drive.google.com/folderview?id=0B4fI_1marvBZR2haeVRrRmJFOEE&usp=sharing

Et créer une machine virtuelle Ubuntu 16.04 LTS :

* https://drive.google.com/folderview?id=0B4fI_1marvBZOU00Y0lWZXRwUm8&usp=sharing

## Prérequis

### - Mise à jour du système

Pour maintenir à jour le système de la machine virtuelle, utilisez la commande suivante :

```
$ sudo apt-get update
```

### - Putty (optionnel)

Si vous êtes intéressés d’utiliser SSH pour vous connectez à la machine virtuelle. Vous pouvez utiliser la commande ssh directement sur Ubuntu ou installer un logiciel tel que Putty pour les PCs contenant Windows 8.1 (et moins).

Dans votre pc, installez Putty :

lien de téléchargement : http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html

Dans la machine virtuelle, installez le serveur OpenSSH pour permettre le contrôle à distance au client SSH Putty :

```
$ sudo apt-get install openssh-server
```

Dans le "Settings" de la VM, mettez le réseau à "Bridged" ou à "NAT" selon votre type de connexion à fin d’avoir une connexion dans la VM.

### - Java

Hadoop est écrit en Java, donc vous aurez besoin d'avoir Java installé sur votre machine (version 7 ou ultérieure).

Pour ce cours, la version 8 de Java sera utilisée via la distribution OpenJDK. Voir commande ci-dessous pour installer OpenJDK 8 sur un Linux.

```
sudo apt-get install openjdk-8-jdk
```

Après l'installation, assurez-vous que la version Java est correctement installée.

```
$ java -version
openjdk version "1.8.0_131"
OpenJDK Runtime Environment (build 1.8.0_131-8u131-b11-2ubuntu1.16.04.3-b11)
OpenJDK 64-Bit Server VM (build 25.131-b11, mixed mode)
```

## Installation

Dans ce cours, nous ciblons la dernière version stable Hadoop 2.7.4 (la version 2.8.1 est encore instable et Hadoop 3.0.0 est encore en version alpha).

Téléchargez le fichier compressé à travers la commande suivante :

```
$ wget http://www.eu.apache.org/dist/hadoop/common/hadoop-2.7.4/hadoop-2.7.4.tar.gz
```

Décompressez l'archive de Hadoop via la commande suivante :

```
$ tar xzf hadoop-2.7.4.tar.gz
```

Déplacez le répertoire hadoop-2.7.4 vers /usr/local sous le nom hadoop :

```
$ sudo mv hadoop-2.7.4 /usr/local/hadoop
```

Avant de pouvoir exécuter Hadoop, vous devez lui dire où Java est situé sur votre système. Si vous avez la variable d'environnement JAVA_HOME mis au point avec une installation de Java appropriée, vous n'avez rien à configurer.

C'est très pratique de créer une variable d'environnement (HADOOP_HOME) qui pointe vers le répertoire d'installation Hadoop et de mettre le répertoire binaire Hadoop sur votre chemin de ligne de commande (PATH). (En Hadoop 2.0 ou ultérieure, vous devrez mettre le répertoire sbin sur le "PATH" aussi).

Seul le fichier de configuration utilisateur .bashrc a besoin d'être modifié afin qu'il propage les nouvelles valeurs des variables d'environnement.

Ouvrez le fichier /home/etudiant/.bashrc (avec la commande « nano » ou « gedit »)

```
$ nano /home/etudiant/.bashrc
```

Puis ajoutez à la fin du fichier les lignes suivantes : 

(PS : Vérifiez le chemin de Java dans votre machine)

```
# Java Environment Variable
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64

# Hadoop Environment Variables
export HADOOP_HOME=/usr/local/hadoop
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib/native"
```

Pour que le système prenne en compte la modification, rechargez le fichier ~/.bashrc :

```
source ~/.bashrc
```

Le fichier $HADOOP_HOME/etc/hadoop/hadoop-env.sh contient des variables d'environnement utilisées par Hadoop. Décommentez (ou modifiez) celle de la variable JAVA_HOME.

```
# The java implementation to use.
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

Vérifiez que Hadoop s'exécute en tapant :

```
$ hadoop version
Hadoop 2.7.4
Subversion https://shv@git-wip-us.apache.org/repos/asf/hadoop.git -r cd915e1e8d9d0131462a0b7301586c175728a282
Compiled by kshvachk on 2017-08-01T00:29Z
Compiled with protoc 2.5.0
From source with checksum 50b0468318b4ce9bd24dc467b7ce1148
This command was run using /usr/local/hadoop/share/hadoop/common/hadoop-common-2.7.4.jar
```

## Configuration

### Fichiers de configuration Hadoop

Tous les fichiers de configuration de Hadoop sont disponibles dans le répertoire /etc/hadoop.

Les fichiers de configuration de Hadoop fonctionnent sur le principe de clé/valeur : la clé correspondant au nom du paramètre et valeur à la valeur assignée à ce paramètre. Ces fichiers de configuration utilisent le format XML. Les nouveaux paramètres sont à ajouter entre la balise :

```
<configuration>

...

</configuration>.
```
 

Chaque propriété de ce fichier est de la forme : 

```
<property> 
  <name>nom de la propriété</name> 
  <value>valeur de la propriété</value> 
 </property>
```

Nous ne pouvons pas être exhaustifs sur les modifications à apporter sur ces fichiers de configuration. Nous nous limiterons donc aux paramètres de base pour exécuter un cluster Hadoop composé d'un noeud. Pour plus d'informations sur les paramètres autorisés, nous vous invitons à consulter les liens que nous donnerons pour chaque fichier modifié.

#### - core-site.xml

Le fichier $HADOOP_HOME/etc/hadoop/core-site.xml est le fichier général de la configuration de la plate-forme. Il contient des informations telles que le numéro de port utilisé par l'instance Hadoop, la mémoire allouée pour le système de fichiers, la limite de mémoire pour stocker les données et la taille des tampons de lecture/écriture.

Dans ce fichier, modifiez le contenu afin d'obtenir le résultat ci-dessous :

```
<configuration>
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://localhost:9000</value>
    <description>The name of the default file system.</description>
  </property>
</configuration>
```

La propriété fs.defaultFS permet de spécifier quant à elle le nom du système de fichier. Hdfs:// localhost/ est utilisé pour définir un système de fichiers par défaut pour Hadoop. Les systèmes de fichiers sont spécifiés par un URI, et ici nous avons utilisé un HDFS URI pour configurer Hadoop à utiliser HDFS par défaut. Les démons HDFS vont utiliser cette propriété pour déterminer l'hôte et le port du NameNode HDFS.

#### - hdfs-site.xml

Le fichier $HADOOP_HOME/etc/hadoop/hdfs-site.xml contient les paramètres spécifiques au système de fichiers HDFS : le nombre de réplication des données, le chemin de NameNode et les chemins des DataNodes dans les systèmes de fichiers locaux. Il désigne l'endroit où vous souhaitez stocker l'infrastructure Hadoop.

```
<configuration>
  <property>
    <name>dfs.replication</name>
    <value>1</value>
  </property>
  <property>
    <name>dfs.namenode.name.dir</name>
    <value>file:/usr/local/hadoop/hadoopdata/hdfs/namenode</value>
  </property>
  <property>
    <name>dfs.datanode.data.dir</name>
    <value>file:/usr/local/hadoop/hadoopdata/hdfs/datanode</value>
  </property>
</configuration>
```

Le paramètre dfs.replication permet de préciser le nombre de réplication d'un block. La valeur sera 1 puisque notre cluster ne se compose que d'un noeud. En effet, nous avons mis cette propriété, à 1 afin que HDFS ne réplique pas le blocs du système de fichiers par le facteur par défaut 3. Lors de l'exécution d'une seule datanode, HDFS ne peut pas reproduire les blocs à trois datanodes, de sorte qu'il soit en avertissement continu sur les blocs étant sous-répliqués. Ce paramètre permet de résoudre ce problème.

La propriété dfs.namenode.name.dir détermine l'endroit où le NameNode de HDFS doit stocker la "name table" (fsimage) sur le système de fichiers local.

La propriété dfs.datanode.data.dir détermine l'endroit où un DataNode de HDFS doit stocker ses blocs sur le système de fichiers local.

Créez les répertoires de NameNode et DataNode et changez le propriétaire du dossier «hadoop» et les dossiers et fichiers contenus à l'intérieur de ce dossier.

```
$ cd
$ mkdir -p /usr/local/hadoop/hadoopdata/hdfs/namenode
$ mkdir -p /usr/local/hadoop/hadoopdata/hdfs/datanode
$ sudo chown etudiant:etudiant -R /usr/local/Hadoop
```

#### - mapred-site.xml

Le fichier $HADOOP_HOME/etc/hadoop/mapred-site.xml contient les paramètres spécifiques à MapReduce. Depuis la version 2.x de Hadoop avec l'arrivée de Yarn, ce fichier de configuration est épaulé par yarn-site.xml. 

Ainsi, si vous souhaitez utiliser Yarn comme implémentation de MapReduce, il faudra tout d'abord renommer/copier le fichier par defaut $HADOOP_HOME/etc/hadoop/mapred-site.xml.template en $HADOOP_HOME/etc/hadoop/mapred-site.xml. Puis, il faudra configurer le fichier mapred-site.xml comme présenté ci-dessous :

```
$ cp $HADOOP_HOME/etc/hadoop/mapred-site.xml.template $HADOOP_HOME/etc/hadoop/mapred-site.xml
```

```
$ nano $HADOOP_HOME/etc/hadoop/mapred-site.xml
```

```
<configuration>
  <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
  </property>
</configuration>
```

#### - yarn-site.xml

Puisque nous visons installer Hadoop v2, nous devons configurer le fichier $HADOOP_HOME/etc/hadoop/yarn-site.xml.

```
<configuration>
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>
  <property>
    <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
    <value>org.apache.hadoop.mapred.ShuffleHandler</value>
  </property>
</configuration>
```

### Configuration SSH

Hadoop nécessite un accès SSH pour gérer les différents noeuds. Bien que nous soyons dans une configuration simple noeud, nous avons besoin de configurer l'accès vers localhost.

Tout d'abord, assurez-vous que SSH est installé et un serveur est en cours d'exécution. Sur Ubuntu, ce résultat est obtenu avec :

```
$ sudo apt-get install ssh
```

Puis, pour activer un login sans mot de passe, générez une nouvelle clé SSH avec un mot de passe vide.

```
$ ssh-keygen -t rsa -P ""

Generating public/private rsa key pair.
Enter file in which to save the key (/home/amal/.ssh/id_rsa):
Created directory '/home/amal/.ssh'.
Your identification has been saved in /home/amal/.ssh/id_rsa.
Your public key has been saved in /home/amal/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:6x4uPYsRJ6/J3bYAGmccyyv+QRFm8UGsYtoSir2e5M8 amal@amal-virtual-machine
The key's randomart image is:
+---[RSA 2048]----+
| =+o |
| o o.. |
| o.. |
| . oo.+ |
|.o =..@ S |
|o + .* B . |
| .oo +.= |
| o.+ o.Xo+. |
| .+.E.*o*+o. |
+----[SHA256]-----+
```

Cette commande va créer une paire de clés RSA (publique/privée) avec un mot de passe vide. Dans notre cas de test et de virtualisation, l'absence de mot de passe n'a pas d'importance. Assurez-vous d'en fixer un si votre serveur est accessible depuis l'extérieur.

Vous devez ensuite autoriser l'accès au SSH de la machine avec cette nouvelle clé fraîchement créée.

```
$ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

La dernière chose à réaliser est de tester la connexion SSH.

```
$ ssh localhost

The authenticity of host 'localhost (127.0.0.1)' can't be established.
ECDSA key fingerprint is SHA256:0Xdp/TXQvQKT9nzZ434ieIvIsbU8f0YbnbVr5wIIZes.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'localhost' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-31-generic x86_64)
* Documentation: https://help.ubuntu.com
* Management: https://landscape.canonical.com
* Support: https://ubuntu.com/advantage
537 paquets peuvent être mis à jour.
272 mises à jour de sécurité.
Last login: Mon Sep 18 21:31:55 2017 from 192.168.32.1
```

En cas de succès, vous ne devriez pas taper un mot de passe.

### Initialisation du système de fichier HDFS

Avant de démarrer le serveur Hadoop, vous devez formater le système de fichiers HDFS. Comme tout système de fichiers, il est nécessaire de le formater avant son utilisation. Dans le cas de cette installation d'un cluster simple noeud, seul le système de fichiers HDFS de votre machine locale sera formaté. Pour cela, il suffit d’exécuter la commande hadoop système suivante :

```
$ hdfs namenode –format
```

L'exécution du formatage devrait ressembler à cela :

```
17/09/18 22:38:54 INFO namenode.NameNode: STARTUP_MSG:
/************************************************************
STARTUP_MSG: Starting NameNode
STARTUP_MSG: host = amal-virtual-machine/127.0.1.1
STARTUP_MSG: args = [–format]
STARTUP_MSG: version = 2.7.4
STARTUP_MSG: classpath = /usr/local/hadoop/etc/hadoop:/usr/local/hadoop/share/hadoop/common/lib/jaxb-api-2.2.2.jar:/usr/local/hadoop/share/hadoop/common/lib/gson-
...
...
...
STARTUP_MSG: build = https://shv@git-wip-us.apache.org/repos/asf/hadoop.git -r cd915e1e8d9d0131462a0b7301586c175728a282; compiled by 'kshvachk' on 2017-08-01T00:29Z
STARTUP_MSG: java = 1.8.0_131
************************************************************/
17/09/18 22:38:54 INFO namenode.NameNode: registered UNIX signal handlers for [TERM, HUP, INT]
17/09/18 22:38:55 INFO namenode.NameNode: createNameNode [–format]
Usage: java NameNode [-backup] |
[-checkpoint] |
[-format [-clusterid cid ] [-force] [-nonInteractive] ] |
[-upgrade [-clusterid cid] [-renameReserved<k-v pairs>] ] |
[-upgradeOnly [-clusterid cid] [-renameReserved<k-v pairs>] ] |
[-rollback] |
[-rollingUpgrade <rollback|downgrade|started> ] |
[-finalize] |
[-importCheckpoint] |
[-initializeSharedEdits] |
[-bootstrapStandby] |
[-recover [ -force] ] |
[-metadataVersion ] ]
17/09/18 22:38:56 INFO namenode.NameNode: SHUTDOWN_MSG:
/************************************************************
SHUTDOWN_MSG: Shutting down NameNode at amal-virtual-machine/127.0.1.1
************************************************************/
```

### Démarrage et arrêt d'un serveur Hadoop

Pour démarrer Hadoop, vous aurez besoin de démarrer le système de fichiers HDFS et le serveur MapReduce dans le cas où vous souhaitez utiliser des jobs MapReduce.

La commande suivante démarre le système de fichiers HDFS.

```
$ start-dfs.sh
```

Affichage obtenu :

```
Starting namenodes on [localhost]
localhost: starting namenode, logging to /usr/local/hadoop/logs/hadoop-amal-namenode-amal-virtual-machine.out
localhost: starting datanode, logging to /usr/local/hadoop/logs/hadoop-amal-datanode-amal-virtual-machine.out
Starting secondary namenodes [0.0.0.0]
0.0.0.0: starting secondarynamenode, logging to /usr/local/hadoop/logs/hadoop-amal-secondarynamenode-amal-virtual-machine.out
```

Il faut ensuite démarrer le serveur MapReduce en exécutant la commande suivante :

```
$ start-yarn.sh
```

Affichage obtenu :

```
starting yarn daemons
starting resourcemanager, logging to /usr/local/hadoop/logs/yarn-amal-resourcemanager-amal-virtual-machine.out
localhost: starting nodemanager, logging to /usr/local/hadoop/logs/yarn-amal-nodemanager-amal-virtual-machine.out
```

Pour s'assurer que tout fonctionne, utiliser l'outil jps pour lister les processus Java en cours d'exécution :

```
$ jps
3631 Jps
3336 NodeManager
3212 ResourceManager
3651 NameNode
2863 DataNode
3052 SecondaryNameNode
```

Depuis la version 2.3.x de Hadoop, ResourceManager remplace JobTracker.

Vous pouvez également vérifier si les démons ont commencé avec succès en regardant fichiers de logs dans le répertoire des logs (dans le répertoire d'installation Hadoop) ou en regardant la bande interfaces, à l'adresse http://localhost:8088/ pour le ResourceManager et à l'adresse http://localhost:50070/ pour la NameNode.

Enfin, pour arrêter un serveur Hadoop, vous devrez utiliser la commande suivante pour interrompre le serveur de fichiers HDFS :

```
$ stop-dfs.sh
```

et la commande suivante pour interrompre le serveur MapReduce :

```
$ stop-yarn.sh
```

## Remarques :

* Pour utiliser la ligne de commande sous HDFS (hadoop fs –[generic option]), n’oublierez jamais de démarrer HDFS (start-dfs.sh).

* Même remarque pour l’utilisation de yarn (start-yarn.sh).

* Si l’interface web du NameNode n’a pas été affichée vous devez vérifier la configuration des fichiers « core-site.xml » et « hdfs-site.xml », et suivre les étapes suivantes : 

  o Lancez la commande : hdfs namenode o Généralement vous aurez comme exception : NameNode is not formatted

  o Lancez les commandes : stop-dfs.sh et stop-yarn.sh
  
  o Vérifiez la configuration des fichiers « core-site.xml » et « hdfs-site.xml ». Cette exception peut être dû à l’oublie de la sauvegarde des modifications nécessaires sur ces fichiers.
  
  o Supprimez le contenu des deux dossiers (le contenu des dossiers, pas les dossiers!) : /usr/local/hadoop/hadoopdata/hdfs/namenode /usr/local/hadoop/hadoopdata/hdfs/datanode

  o Refaites le formatage : hdfs namenode –format -force
  
  o Lancez les commandes : start-dfs.sh et start-yarn.sh
  
  o Lancez la commande : jps

  Le namenode et le datanode doivent apparaitre dans la liste.
  
  o Consultez la page http://localhost:50070/



