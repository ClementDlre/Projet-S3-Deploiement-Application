
## 1 Dernière configurations sur la VM

### 1.1 Changement du nom de machine

!!! warning Remarque
    Utiliser l'utilisateur root !

!!! note Modification du fichier hostname : ~~debian~~ 	-->  matrix

    ```bash
    (vm)root@debian:~# nano /etc/hostname
    ```

    <img src="../images/hostname.png">

     ```bash
    (vm)root@matrix:~# reboot
    ```

!!! note Modification du fichier hosts (Domain name system) :  ~~debian~~ -->  matrix

    ```bash
    (vm)root@matrix:~# nano /etc/hosts
    ```

    <img src="../images/hostname.png">

    ```bash
    (vm)root@matrix:~# reboot
    ```


```bash
(vm)root@matrix:~# ping matrix
```

### 1.2 Installation et configuration de la commande `sudo`

```bash
(vm)root@matrix:~# apt-get install sudo
```

```bash
(vm)root@matrix:~# nano /etc/sudoers
```

```bash
(vm)root@matrix:~# usermod -aG sudo user
```

!!! success Verification : User est bien ajouté au groupe sudo

    ```bash
    (vm)user@matrix:~# groups
    ```

### 1.3 Configuration de la synchronisation d’horloge

```bash
(vm)user@matrix:~# date
lun. 21 nov. 2022 15:18:38 CET
```

```bash
(virtu)utilisateur@acajou12:~$ date
lun. 21 nov. 2022 14:18:38 CET
```

```bash
(phy)utilisateur@hevea19:~$  date
lun. 21 nov. 2022 14:18:38 CET
```

!!! note Modification du fichier timesyncd.conf
    Décommenter les lignes en-dessous de [Time] et ajouter `NTP=ntp.univ-lille.fr`

    ```bash
    (vm)user@matrix:~# nano /etc/systemd/timesyncd.conf
    ```

    <img src="../images/timesyncd.png">



```bash
(vm)user@matrix:~# timedatectlset-ntp true
(vm)user@matrix:~# timedatectl status
(vm)user@matrix:~# systemctl restart systemd-timesyncd.service
```

!!! info Informations

    Il faut attendre quelques secondes pour afficher la bonne heure au lancement de la vm