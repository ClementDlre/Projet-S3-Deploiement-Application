## - Machine virtuelle rproxy -

### Création et configurations

!!! info Rappel

    La machine virtuelle rproxy a déjà été créee et configurée : [Procédure rproxy](../TP04/reverse_proxy.md)


!!! info Informations

    Il nous reste qu'à faire quelques modifications pour l'architecure finale

!!! note Modification du fichier hosts 

    ```bash
    (vm)root@debian:~# nano /etc/hosts 
    ```                         
        127.0.0.1       localhost
        127.0.1.1       rproxy
        192.168.194.3   matrix
        192.168.194.5   db
        192.168.194.6   element

!!! note Modification de la configuration nginx

    ```bash
    (vm)root@debian:~# nano /etc/nginx/site-available/default
    ```                                                                     
        server {
                listen 9090;
                listen [::]:9090;

                server_name acajou12.iutinfo.fr;

                location / {
                        proxy_pass http://192.168.194.3:9090;
                        proxy_set_header Host $http_host;
                        proxy_set_header X-Real-IP $remote_addr;
                        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                        proxy_set_header X-Forwarded-Proto $scheme;
                }
        }

        server {
                listen 9090;
                listen [::]:9090;

                server_name acajou12.iut-infobio.priv.univ-lille1.fr;

                location / {
                        proxy_pass http://192.168.194.6:8080;
                        proxy_set_header Host $http_host;
                        proxy_set_header X-Real-IP $remote_addr;
                        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                        proxy_set_header X-Forwarded-Proto $scheme;
                }
        }




!!! tip Lien de la prochaine procédure

    [Alias](./6_alias.md)