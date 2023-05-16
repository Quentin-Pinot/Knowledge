<h1 align="center">Apache HDFS</h1>

# Présentation

_Hadoop Distributed File System_

Il a été crée par Hadoop mais est adapté aussi à des utilisations externes. Son utilisation est semblable à un système de fichiers basique mais son architecture est distribuée. 

Le schéma classique fonctionne grâce au principe de maître/esclaves. Les datanodes (esclaves) stockent les données et le namenode (maître) répertorie le noms des fichiers et les adresses des blocs de données.

Les fichiers sont décomposé en blocs de taille max fixe qui est de 64 Mo par défaut. Si un fichiers a une taille supérieure alors il sera décomposé en plusieurs parties.

La limite de stockage de HDFS est de 512 Yo (512 x 10^12 To) et le nombre de fichiers par répertoire est de 1048576 par défaut (géré par le paramètre : fs.namenode.fs-limits.max-directory-items).

![HDFS schéma réplication 2](https://user.oc-static.com/upload/2017/08/03/15017750983343_hdfs-architecture.jpeg)

La réplication des données est à 2 ce qui implique que chaque bloc est dupliqué 2 fois sur l'esemble des data nodes.

![HDFS lecture d'un fichier](https://user.oc-static.com/upload/2017/08/03/15017751645123_hdfs-read.jpeg)

Voici le cheminement d'une demande de lecture d'un fichier sur HDFS.

![HDFS Ecriture d'un fichier](https://user.oc-static.com/upload/2017/08/03/15017753323082_hdfs-write.jpeg)

Démontration du cheminement de l'écriture d'un fichier sur HDFS.

HDFS est un système de stockage de données qui garanti d'après le théorème du CAP la Cohérence et la Distribution. La distribution permet de minimiser l'impact d'une indisponibilité vu que la donnée est répliquée sur plusieurs nodes et la cohérence la dernière version.

![Exemple du CAP sur des SGBD](https://user.oc-static.com/upload/2017/08/14/15027166454802_cap.png)

Le positionnement de différent SGBD en fonction du théorème de CAP.
Rappel : Un système de stockage de données ne peut garantir les trois sommets du triangle

HDFS est programmé en Java.


## Installation

### Via une [VM Ubuntu Server](https://hibbard.eu/install-ubuntu-virtual-box/)

#### Créer des VMs - Ubuntu Server

Les VMs que je vais utiliser pour la partie d'HDFS ont été déployé par rapport à cette documentation d'[Oracle VM Virtual Box](/Informatique/Oracle/Oracle_VM_Virtual_Machine.md) qui sont des Ubuntu Servers. **Attention on doit garder le même nom d'utilisateur pour tous les workers d'HDFS**

Nous allons créer une VM par **NameNode** et par **DataNode**.
Pour assurer le bon fonctionnement d'HDFS, nous allons devoir configurer des connexions ssh par clé public/privéf et désactiver la connexion par mdp ou passphrase.
1. Créer la clé ssh public/privé (A faire pour toutes les VMs) :
```shell
ssh-keygen -t rsa -P ''
```
2. Copier la clé ssh public key depuis une vm dans une autre :
```shell
ssh-copy-id -i ~/.ssh/id_rsa.pub namenode@IP_Adress_autre_node
```
3. Sauvegarder sa propre ssh public key
```shell
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```
4. Vérifier la connexion à chaque host distant et aussi en localhost !

#### Installer JAVA 

1. JRE 
```shell
sudo apt-get install default-jre
```
2. Ecrire l'output de la commande bash sans le **/bin/java**
```shell
readlink -f /usr/bin/java
```
dans le fichier **~/.bashrc** en remplacant le terme readlink et lancer à nouveau la 1ere ligne en commande linux
```shell
export JAVA_HOME=readlink 
export PATH=$JAVA_HOME/bin:$PATH
```
3. La commande **jps** signifie **Java process status** et permet de lister les futurs processus java de lancer (commme les nodes d'hdfs)

#### Installer HDFS

1. Récupérer les [binaires d'Hadoop](https://hadoop.apache.org/releases.html) de la version qui nous interesse
```shell 
wget https://dlcdn.apache.org/hadoop/common/hadoop-3.3.5/hadoop-3.3.5.tar.gz
```
2. Extraire les fichiers compressé
```shell 
tar xzf fichier_compresse
```

#### Configuration

1. Ajouter dans la partie configuration du fichier **~/hadoop_version/etc/hadoop/core-site.xml** :
```xml
<property>
    <name>fs.defaultFS</name>
    <value>hdfs://@ip_namenode:9000</value>
</property>
```
2. Créer les dossiers NameNode **~/hdfs/namenode** et DataNode **~/hdfs/datanode**
3. Modifier les fichiers suivant du dossier d'installation **~/hadoop_version**
   1. **etc/hadoop/hdfs-site.xml** :
```xml
<property>
    <name>dfs.name.dir</name>
    <value>/home/user/code/hdfs/namenode/</value>
</property>
<!--This section isn't mandatory and not really recommended because distributed means to not put a datanode with the namenode-->
<property>
    <name>dfs.data.dir</name>
    <value>/home/user/code/hdfs/datanode/</value>
</property>
<property>
    <!--The default replication is set to 3-->
    <name>dfs.replication</name>
    <value>3</value>
</property>
```
   2. Uniquement pour le **Namenode** : **etc/hadoop/workers**
```xml
@ip_node1
@ip_node2
@ip_nodex
...
```
   3. Ajouter l'endroit ou est installer **JAVA_HOME** pour les fichiers : 
      1. **etc/hadoop/mapred-env.sh** (penser à décommenter aussi la ligne avec **export HADOOP_MAPRED_ROOT_LOGGER=INFO,RFA**)
      2. **etc/hadoop/hadoop-env.sh**
      3. **etc/hadoop/yarn-env.sh**
   4. Ajouter dans la balise **configuration** du fichier **etc/hadoop/yarn-site.xml** :
   ```xml
    <property>
        <name>yarn.nodemanager.aux-services</name> <value>mapreduce_shuffle</value>
    </property>
    <property>
            <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
            <value>org.apache.hadoop.mapred.ShuffleHandler</value>
    </property>
    <property>
            <description>The hostname of the RM.</description>
            <name>yarn.resourcemanager.hostname</name>
            <value>@IP_NameNode</value>
    </property>
    <property>
            <description>The address of the applications manager interface in the RM.</description>
            <name>yarn.resourcemanager.address</name>
            <value>@IP_NameNode:8032</value>
    </property>
   ```
   5. Ajouter dans la balise **configuration** du fichier **etc/hadoop/mapred-site.xml**
   ```xml
   <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
   ```
5. Formater le NameNode
```shell
bin/hdfs namenode -format
```
6. Lancement de notre cluster HDFS
```sh
sbin/start-dfs.sh
``` 

#### Configurer une autre DataNode

1. Exemple de fichier config pourne une DataNode plus spécifique **etc/hadoop/hdfs-site.xml** :
```xml
<configuration>
    <property>
        <!--Give the port and @IP of the datanode-->
        <name>dfs.datanode.address</name>
        <value>@IP:50011</value>
    </property>
    <property>
        <!--Give the port and @IP of the website's page-->
        <name>dfs.datanode.http.address</name>
        <value>@IP:50076</value>
    </property>
    <property>
        <!--Give the port and @IP of the IPC service-->
        <name>dfs.datanode.ipc.address</name>
        <value>@IP:50021</value>
    </property>
    <property>
        <!--Data's path on the disk
        We need to create it-->
        <name>dfs.data.dir</name>
        <value>/home/user/code/hdfs/datanode2</value>
    </property>
</configuration>
```
2. Ajouter les mêmes étapes que précédement
3. Sur le **NameNode** nous devons rafraichir sa connaissance de ses **DataNode**
```shell
bin/hdfs dfsadmin -refreshNodes
```
4. Pour lancer la **DataNode** sur sa machine lancer la commande (**&** : pour ne pas bloquer le terminal). On peut même spécifier un fichier en particulier de configuration sinon il se bassera sur **etc/hadoop/hdfs-site.xml**
```shell
bin/hdfs datanode -conf &
```
5. La commande suivante nous permet de voir des informations sur un fichier dans le cluster
```shell
bin/hdfs fsck /file -files -blocks -locations
```
6. 


[IPC](https://cwiki.apache.org/confluence/display/HADOOP2/ipc) : InterProcess Communication

#### Commniquer avec Python

Il est important de le faire à partir d'un ordinateur qui a sa clé ssh public dans le fichier authorized_host de la machine du namenode.

1. Installer le package HDFS
```shell
pip install hdfs
```
2. Connection au web-server HDFS (Dans les logs à Stating Web-server for hdfs at: ...)
```python
import hdfs

client = hdfs.InsecureClient("http://@IP_Namenode:9870")
```
3. Afficher le contenu à la racine
```python
client.list('/')
```
4. Ecrire un fichier txt avec le contenu au format binaire
```python
with client.write("/test.txt") as f:
    f.write(b"hello world !")
```
5. Lire un fichier
```python
with client.read("/test.txt") as f:
    print(f.read())
```
6. Upload un fichier
```python
client.upload('path_hdfs', 'path_file/file_pload')
```
7. 

### Via [Docker Compose](https://towardsdatascience.com/hdfs-simple-docker-installation-guide-for-data-science-workflow-b3ca764fc94b)