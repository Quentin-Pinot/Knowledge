<h1 align="center">Data Lake</h1>

![Data Lake exemple](https://user.oc-static.com/upload/2017/08/14/15027147514724_datalake.png)


Contient un ensemble de données très variées :
    
    - Logs
    - Binaires
    - Images
    - Etc...

Le point de référence du Data Lake est son **Master Dataset** :

    - Données écrites une seule fois
    - Ajouts uniquement
    - Lectures nombreuses
    - Lecture séquentielle

Le dataset croîtra dans le temps ce qui implique d'utiliser une solution de stockage peu coûteuse.

Afin de pouvoir passer à l'échelle, le stockage se fera de manière distribuée.

La volumétrie des données n'implique pas forcément le besoin d'offrir une vitesse d'écriture élevée.

Un Système de fichiers distribué stocke ses données sur plusieurs machines. Théoriquement il suffirait d'ajouter une machine pour adapter à l'échelle.





