## - Machine virtuelle element -

### Création de la machine virtuelle element

```bash
(virtu)utilisateur@acajou12:~$ vmiut creer element
```

### Configuration de element

!!! note Interface réseau : Modification de l'adresse ip

    ```bash
    root@vm#  ifdown enp0s3
    ```

    ```bash
    root@vm# nano /etc/network/interfaces
    ```
        iface enp0s3 inet static
            address 192.168.194.6/24
            gateway 192.168.194.2
    
    ```bash
    root@vm# ifup enp0s3
    root@vm# reboot
    ```


```bash
(virtu)utilisateur@acajou12:~$ ssh user@192.168.194.6
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

!!! note Nom hôte et fichier de résolution : Modification des fichiers hostname et hosts (debian -> element)

    ```bash
    (vm)root@debian:~# nano /etc/hostname
    ```
    
    ```bash
    (vm)root@debian:~# nano /etc/hosts
    ```
    127.0.0.1       localhost
    127.0.1.1       element
    192.168.194.3   matrix
    192.168.194.4   rproxy
    192.168.194.5   db


!!! note Installation de nginx

    ```bash
    (vm)root@element:~# apt-get install nginx
    ```

!!! note Installation de curl 

    ```bash
    root@element:~# apt-get install curl
    ```

!!! tip Fonction tunnel de ssh

    ```bash
    (virtu)utilisateur@acajou12:~$ ssh -L 0.0.0.0:8080:localhost:80 user@192.168.194.6
    ```

!!! note Création et modification du fichier element

    ```bash 
    root@element# cd /etc/nginx/sites-available
    ```

     ```bash 
    root@element:/etc/nginx/sites-available# touch element
    ```

    ```bash 
    root@element:/etc/nginx/sites-available# nano element
    ```

    ```vim
        server {
            listen 8080;
            listen [::]:8080;
            root /var/www/html/element;

            index index.html welcome.html

            server_name _;

            location / {
                try_files $uri $uri/ = 404;
            }
        }
    ```

!!! note Création d'un lien symbolique dans sites enabled

     ```bash 
    root@element:/etc/nginx/sites-enabled# unlink default
    ```

    ```bash 
    root@element:/etc/nginx/sites-enabled# ln -s /etc/nginx/sites-available/element /etc/nginx/sites-enabled/element
    ```

!!! note Installation du client Element

      ```bash
        root@element# curl -s https://api.github.com/repos/vector-im/element-web/releases/latest \
        | grep 'browser_download_url.*tar.gz"' \
        | cut -d : -f 2,3 \
        | tr -d \" \
        | wget -O element -qi -
        ```

!!! note Décompresser le fichier element (tar.gz) directement dans le serveur Nginx

    ```bash
    root@element# tar -xzvf element -C /var/www/html/ 
    ```

!!! note Creation du fichier config.json (copie de config.sample.json)

     ```bash 
    root@vm# cd /var/www/html/element
    ```

    ```bash 
    root@vm:/var/www/html/element# cp config.sample.json config.json
    ```

    ```bash 
    root@vm:/var/www/html/element#  systemctl restart nginx.service
    ```



!!! tip Lien de la prochaine procédure

    [Reverse proxy final](./5_reverse_proxy_final.md)