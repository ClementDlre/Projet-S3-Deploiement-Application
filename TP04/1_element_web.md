# 1 Element web

!!! info Informations
    Element web est un client matrix qui fontionne uniquemment en web front. Il n'y a donc pas de serveur d'application qui execute Element.
    La distribution d'Element est donc une simple archive contenant des fichiers web statiques. Pour le rendre disponible à un utilisateur il faut donc servir ces fichiers grâce à un vrai serveur web.

    !!! tip 2 principales solutions 
        - Nginx
        - Apache

## 1.1 nginx vs apache

!!! info Nginx et Apache
    Nginx et Apache sont des serveurs web populaires utilisés pour fournir des pages au navigateur d’un utilisateur. 
    
!!! info Nginx vs Apache 
    |                               | Apache          | Nginx                                                    |
    | ----------------------------- | --------------- | -------------------------------------------------------- |
    | Année                         | 1995            | 2004                                                     |
    | Simplicité                    | Facile          | Complexe                                                 |
    | Performance contenu static    | Lent            | 2,5 x plus rapide qu'apache et consomme moins de mémoire |
    | Performance contenu dynamique | excellent       | excellent                                                |
    | OS                            | Linux - Windows | Linux - Windows (moins stable sur Windows)               |
    | Support et documentation      | Excellent       | Bonne évolution depuis le lancement                      |

!!! tip Conclusion
    Apache et Nginx sont tous les deux performants et flexibles. Ils sont très utilisés et l'utilisation de l'un ou l'autre dépend surtout des utilisations spécifiques. Par exemple pour du contenu statique, nginx sera meilleur qu'Apache.
    
    Dans notre cas, nous choisissons nginx car nous l'avons déjà installé, de plus nginx semble plus performant.

    !!! info Source
        https://hackr.io/blog/nginx-vs-apache

# 1.2 Installation et configuration de Nginx

!!! info Rappel
    Nous avons déjà installé Nginx plus tôt mais la commande pour l'installation est :

    ```bash
    (vm)root@matrix:~# apt-get install nginx
    ```

    ```bash 
    root@vm# reboot
    ```


    ```bash 
    root@vm# systemctl status nginx
    ```

!!! success nginx.service
    Active : active (running)

!!! info Accès aux fichiers de configuration
    ```bash 
    root@vm# cd /etc/nginx/sites-available
    ```

!!! note Création du fichier de configuration element

    ```bash 
    root@vm:/etc/nginx/sites-available# touch element
    ```

    ```bash 
    root@vm:/etc/nginx/sites-available# nano element
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

!!! note Créer un lien symbolique dans le dossier sites-enabled vers le fichier de configuration element

    ```bash 
    root@vm:/etc/nginx/sites-enabled# unlink default
    ```

    ```bash 
    root@vm:/etc/nginx/sites-enabled# ln -s /etc/nginx/sites-available/element /etc/nginx/sites-enabled/element
    ```

# 1.3 Installation du client Element

!!! note Télecharger le fichier tar.gz de Element 

    !!!note Pour télécharger la version v1.11.16
        
        1 - Clique droit sur element-v1.11.16.tar.gz et ouvrir un nouvel onglet
        2 - copier le lien : https://github.com/vector-im/element-web/releases/download/v1.11.16/element-v1.11.16.tar.gz
        3 - Sur la vm :
        ```bash
        root@vm# wget -O element https://github.com/vector-im/element-web/releases/download/v1.11.16/element-v1.11.16.tar.gz 
        ```
    
    !!! tip Pour télécharger la dernière version automatiquement    
        
        ```bash
        root@matrix$ curl -s https://api.github.com/repos/vector-im/element-web/releases/latest \
        | grep 'browser_download_url.*tar.gz"' \
        | cut -d : -f 2,3 \
        | tr -d \" \
        | wget -O element -qi -
        ```

!!! note Décompresser le fichier tar.gz directement dans le serveur Nginx
    ```bash
    root@vm# tar -xzvf element -C /var/www/html/ 
    ```

!!! warning Creation du fichier config.json

      On copie le fichier config.sample.json vers un nouveau fichier qu'on nomme config.json

     ```bash 
    root@vm# cd /var/www/html/element
    ```

    ```bash 
    root@vm:/var/www/html/element# cp config.sample.json config.json
    ```


