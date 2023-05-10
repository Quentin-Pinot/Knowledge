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

#### Installer JAVA 

   1. JRE 
    ```shell
    sudo apt-get install default-jre
    ```
   2. Ecrire l'output de la commande bash sans le **/bin/java**
    ```shell
    readlink -f /usr/bin/java
    ```
    dans le fichier **~/.bashrc** en remplacant le terme readlink
    ```shell
    export JAVA_HOME=readlink && export PATH=$PATH:JAVA_HOME
    ```

#### Installer HDFS

   1. Récupérer les [binaires d'Hadoop](https://hadoop.apache.org/releases.html) de la version qui nous interesse
    ```shell 
    wget link_dl
    ```
   2. Extraire les fichiers compressé
    ```shell 
    xzf fichier_compresse
    ```

#### Configuration

   1. Ajouter dans la partie configuration du fichier **~/hadoop_version/etc/hadoop/core-site.xml** :
    ```xml
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
    ```
   2. Créer les dossiers NameNode **~/hdfs/namenode** et DataNode **~/hdfs/datanode**
   3. Ajouter dans la partie configuration du fichier **~/hadoop_version/etc/hadoop/hdfs-site.xml** :
    ```xml
    <property>
        <name>dfs.name.dir</name>
        <value>/home/user/code/hdfs/namenode/</value>
    </property>
    <property>
        <name>dfs.data.dir</name>
        <value>/home/user/code/hdfs/datanode/</value>
    </property>
    ```
   4. Formater le NameNode
    ```shell
    ./hadoop_version/bin/hdfs namenode -format
    ```
   5. Lancement de notre NameNode
    ```shell
    ./hadoop_version/bin/hdfs namenode
    ```
   6. Lancement de notre NameNode
    ```shell
    ./hadoop_version/bin/hdfs namenode
    ```
   7. Lancement de notre DataNode
    ```shell
    ./hadoop_version/bin/hdfs datanode
    ```

#### Commniquer avec Python

1. Installer `pip install hdfs`
2. Connection au web-server HDFS (Dans les logs à Stating Web-server for hdfs at: ...)
```python
import hdfs

client = hdfs.InsecureClient("http://0.0.0.0:9870")
```
3. Afficher le contenu à la racine
```python
client.list('/')
```
4. Ecrire un fichier txt avec le contenu au format binaire
```python
client.list('/')
```
5. Lire un fichier

### Via [Docker Compose](https://towardsdatascience.com/hdfs-simple-docker-installation-guide-for-data-science-workflow-b3ca764fc94b)

