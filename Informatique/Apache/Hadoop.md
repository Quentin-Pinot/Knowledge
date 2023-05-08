![Apache Hadoop logo](https://upload.wikimedia.org/wikipedia/commons/3/38/Hadoop_logo_new.svg)

<h1 align="center">Apache Hadoop</h1>

## Sommaire

- Présentation
- HDFS
- Liens

### HDFS

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



### Liens

___

- [HDFS](https://www.lebigdata.fr/hdfs-fonctionnement-avantages)
- [Hadoop infos](https://datascientest.com/hadoop)

___

