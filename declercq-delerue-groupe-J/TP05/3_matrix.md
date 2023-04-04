## - Machine virtuelle matrix -

### Création de la machine virtuelle matrix

!!! info Informations

    On doit supprimer l'ancienne machine virtuelle matrix et la recréer !

```bash
(virtu)utilisateur@acajou12:~$ source /home/public/vm/vm.env 
(virtu)utilisateur@acajou12:~$ vmiut creer matrix
```

```bash
(phy)utilisateur@hevea19:~$ ssh -X virt
```

```bash
(virtu)utilisateur@acajou12:~$ vmiut demarrer matrix
(virtu)utilisateur@acajou12:~$ vmiut console matrix
```

### Configuration de matrix

!!! note Interface réseau : Modification de l'adresse ip

    ```bash
    root@vm#  ifdown enp0s3
    ```

    ```bash
    root@vm# nano /etc/network/interfaces
    ```
        iface enp0s3 inet static
            address 192.168.194.3/24
            gateway 192.168.194.2
    
    ```bash
    root@vm# ifup enp0s3
    root@vm# reboot
    ```



```bash
(virtu)utilisateur@acajou12:~$ ssh user@192.168.194.3
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
        NO_PROXY=localhost,192.168.194.5,192.168.194.6



!!! note Installation de sudo + permissions sudo à user

    ```bash
    (vm)root@debian: apt update && apt full-upgrade
    ```

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

!!! note Nom hôte et fichier de résolution : Modification des fichiers hostname et hosts (debian -> matrix)

    ```bash
    (vm)root@debian:~# nano /etc/hostname
    ```
    
    ```bash
    (vm)root@debian:~# nano /etc/hosts
    ```
    127.0.0.1       localhost
    127.0.1.1       matrix
    192.168.194.4   rproxy
    192.168.194.5   db
    192.168.194.6   element

!!! note Installation de synapse
    ```bash 
    root@matrix# apt install -y lsb-release wget apt-transport-https
    root@matrix# wget -qO /usr/share/keyrings/matrix-org-archive-keyring.gpg https://packages.matrix.org/debian/matrix-org-archive-keyring.gpg
    root@matrix# echo "deb [signed-by=/usr/share/keyrings/matrix-org-archive-keyring.gpg] https://packages.matrix.org/debian/ $(lsb_release -cs) main" | tee /etc/apt/sources.list.d/matrix-org.list
    root@matrix# apt-get update -y
    root@matrix# apt-get install matrix-synapse-py3 -y
    ```
    La valeur de configuration de server_name sera virtu.iutinfo.fr:9090 (en remplaçant virtu par le nom de votre machine de virtualisation)

!!! note Configuration synapse avec db

    ```bash 
    root@matrix# nano /etc/matrix-synapse/homeserver.yaml
    ```
    ```yaml
        pid_file: "/var/run/matrix-synapse.pid"
        listeners:
          - port: 9090
            tls: false
            type: http
            x_forwarded: true
            bind_addresses: ['::1', '127.0.0.1', '192.168.194.3']
            resources:
              - names: [client, federation]
                compress: false
        database:
          name: psycopg2
          args:
            user: synapse_user
            password: synapse_user
            database: synapse
            host: 192.168.194.5
            cp_min: 5
            cp_max: 10
        log_config: "/etc/matrix-synapse/log.yaml"
        media_store_path: /var/lib/matrix-synapse/media
        signing_key_path: "/etc/matrix-synapse/homeserver.signing.key"
        trusted_key_servers: []
        registration_shared_secret: "renan"
        enable_registration_without_verification : true
        enable_registration : true

    ```
    !!! warning Rappel
        Attention à l'indentation du fichier yaml
        Erreur possible si l'indentation (de 2 espaces pour les arguments) n'est pas respectée
    
    !!! danger Etapes antérieures à valider pour faire la suite
        Il faut avoir modifier les fichiers pg_hba et postgres.conf sur la machine db ([procédure création et configuration de la machine virtuelle db](./db.md))

     ```bash 
    root@matrix# systemctl restart matrix-synapse
    root@matrix# register_new_matrix_user -u clement -p clement -a -c /etc/matrix-synapse/homeserver.yaml 
    root@matrix# register_new_matrix_user -u renan -p renan -a -c /etc/matrix-synapse/homeserver.yaml 
    ```

!!! tip Lien de la prochaine procédure

    [Element](./4_element.md)