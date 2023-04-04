## - Machine virtuelle db -

### Creation de la machine virtuelle db 

```bash
(virtu)utilisateur@acajou12:~$ vmiut creer db
```

### Configuration de db

!!! note Interface réseau : Modification de l'adresse ip

    ```bash
    root@vm#  ifdown enp0s3
    ```

    ```bash
    root@vm# nano /etc/network/interfaces
    ```
        iface enp0s3 inet static
            address 192.168.194.5/24
            gateway 192.168.194.2
    
    ```bash
    root@vm# ifup enp0s3
    root@vm# reboot
    ```


```bash
(virtu)utilisateur@acajou12:~$ ssh user@192.168.194.5
```

```bash
(vm)user@vm# su -l
```

!!! note Proxy : Configuration du proxy

    ```bash
    (vm)root@debian : nano /etc/environment
    ```
        HTTP_PROXY=http://cache.univ-lille.fr:3128
        HTTPS_PROXY=http://cache.univ-lille.fr:3128
        http_proxy=http://cache.univ-lille.fr:3128
        https_proxy=http://cache.univ-lille.fr:3128
        NO_PROXY=localhost,192.168.194.3

```bash
(vm)root@debian: apt update && apt full-upgrade
```

!!! note Installation de sudo + permissions sudo à user
    ```bash
    (vm)root@debian:~# apt-get install sudo
    ```

    ```bash
    (vm)root@debian:~# usermod -aG sudo user
    ```

    ```bash
    (vm)root@debian:~# reboot
    ```

!!! note NTP : Configuration et mise à jour de la date
    
    ```bash
    (vm)root@debian:~# nano /etc/systemd/timesyncd.conf
    ```
        [Time]
        NTP=ntp.univ-lille.fr
        FallbackNTP=0.debian.pool.ntp.org 1.debian.pool.ntp.org 2.debian.pool.ntp.org 3>
        RootDistanceMaxSec=5
        PollIntervalMinSec=32
        PollIntervalMaxSec=2048
    
    ```bash
    (vm)root@debian:~# timedatectl set-ntp true
    (vm)root@debian:~# timedatectl status
    (vm)root@debian:~# systemctl restart systemd-timesyncd.service
    ```

!!! note Nom hôte et fichier de résolution : Modification des fichiers hostname et hosts (debian -> db)

    ```bash
    (vm)root@debian:~# nano /etc/hostname
    ```
    
    ```bash
    (vm)root@debian:~# nano /etc/hosts 
    
    ```                         
        127.0.0.1       localhost
        127.0.1.1       db
        192.168.194.3   matrix
        192.168.194.4   rproxy
        192.168.194.6   element


!!! note Installation postgresql et manipulation sur la database
    ```bash
    root@db:~# apt-get install postgresql
    root@db:~# systemctl status postgresql.service
    root@db:~# sudo -u postgres -i
    postgres@db:~$ createuser -d -r -l -P synapse_user 
    postgres@db:~$ createdb --encoding=UTF8 --locale=C --template=template0 --owner=synapse_user synapse
    ```

    !!! info
        > user : synapse_user > mdp : synapse_user
        > database : synapse > owner = synapse_user
    
    !!! warning Changer d'utilisateur
        Pour les commandes suivantes, executer les commandes avec l'utilisateur user et non pas postgres 
    
    ```bash
    user@db:~$ w localhost -U synapse_user -d synapse
    ```

!!! note Modifier les fichier pg_hba.conf et postgresql.conf

    ```bash
    root@db:~# nano /etc/postgresql/13/main/pg_hba.conf 
    ```

    Ajouter cette ligne à la fin :

    ```conf
        host    synapse     synapse_user     192.168.194.3/24        md5
    ```

     ```bash
    root@db:~# nano /etc/postgresql/13/main/postgresql.conf 
    ```

    Modifier la ligne listen_addresses :
    ```conf
        listen_addresses = '192.168.194.3, 192.168.194.5, 192.168.194.6'          # what IP address(es) to listen on;
    ```

    ```bash
    root@db:~# systemctl restart postgres
    ```

!!! tip Lien de la prochaine procédure

    [Matrix](./3_matrix.md)