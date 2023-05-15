

# Ubuntu Server

1. Créer l'image
2. Paramètre Oracle VM Virtualbox :
   1. Stockage > Controle IDE > Image de la version Ubuntu Server
      1. Indiquer sur quel support la VM doit booter pour installer son OS
   2. Réseau > Accès par pont
      1. Cette option va permettre d'autoriser la VM à avoir son @IP sur le réseau
      2. Nous aurons besoin de l'authentifier à l'avenir pour faire les connexions d'HDFS
3. Lancer la VM à partir de l'interface graphique 
4. Remplir les champs :
   1. Id machine
   2. Utilisateur 
5. Installer OpenSSH
6. Installer le package **net-tools**
```shell
sudo apt install net-tools
```
7. Récupérer l'@IP de la VM avec la commande **ipconfig**
8. Une fois terminé, fermer la VM.
9. Ouvrir un cmd 
10. Ajouter aux variables d'environnement pour le Path de l'utilisateur : **C:\Program Files\Oracle\VirtualBox**
11. Afin de lancer la VM en background, lancer la commande :  
```shell
VBoxManage startvm "nom_vm" --type headless
```
12. Ensuite il faudra nous y connecter en ssh avec le nom de l'utilisateur et son IP sur le réseau :
```shell
ssh user@ip_adress
```
14. Il faudra ensuite lancer les commandes suivantes afin de mettre à jour nos packages 
```shell
sudo apt update
sudo apt upgrade
```
15.  Si on souhaite l'éteindre il faudra lancer : 
```shell
VBoxManage controlvm "nom_vm" poweroff
```