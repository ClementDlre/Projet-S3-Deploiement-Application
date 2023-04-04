
## - Configurer et mettre à jour la machine virtuelle -

### Connexion root et SSH

!!! question Questions
    Essayez de vous connecter à la machine virtuelle en SSH sur le compte root.

    -    **Quelle commande avez vous utilisée ?**

            ```bash
            (virtu)utilisateur@acajou12:~$ ssh root@192.168.194.3
            ```

    - **Que se passe-t’il ?** On a les permissions refusées

        ```bash
        (virtu)utilisateur@acajou12:~$ ssh root@192.168.194.3
        The authenticity of host '192.168.194.3 (192.168.194.3)' can't be established.
        ECDSA key fingerprint is SHA256:SUHhxVJVZFiBQ6/koNbZfA9reKHyzIrvPgJvOEJ8zuE.
        Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
        Warning: Permanently added '192.168.194.3' (ECDSA) to the list of known hosts.
        root@192.168.194.3's password: 
        Permission denied, please try again.
        root@192.168.194.3's password: 
        ```

    - **Pourquoi ?** Il faut se connecter avec user et ensuite devenir super utilisateur 

    ```bash
    (virtu)utilisateur@acajou12:~$ ssh user@192.168.194.3
    ```

    ```bash
    user@192.168.194.3's password: user
    ```

    !!!info Informations
        ```bash
        su -l  : --login ou -l -> Exécute un script de connexion pour passer à un nom d’utilisateur spécifique. Vous devrez entrer un mot de passe pour cet utilisateur.
        ```

    !!! note Devenir super utilisateur (root)
        ```bash
        su -l 
        password : root
        ```

### Accès extérieur pour les VM

#### Un peu de réseau

!!! info Informations
    Pour que vos VM aient accès à l’extérieur de ce réseau, elles passent par le routeur 192.168.194.2. Ce routeur est équivalent à la box internet que vous pouvez avoir chez vous. Il ne fait pas qu’un simple routage mais de la translation d’adresse (ou NAT: Network Address Translation en anglais).

    Le principe du NAT est que le routeur remplace l’adresse des machines qui veulent sortir du réseau par son adresse publique (son adresse qui lui permet, lui, de sortir de son réseau). Quand votre machine virtuelle se connecte sur une machine extérieure au réseau virtuel principal (à une autre machine de salle de TP par exemple), celle ci pensera que c’est le routeur (et donc la machine de virtualisation) qui la contacte directement


!!! bug Accès internet non configuré
    ```bash
    user@vm $ wget https://www.framasoft.org
    ```
    **Cela ne fonctionne pas encore car il faut configurer le proxy !**

#### Configuration du proxy

!!! note Modifier le fichier environment
    ```bash
    (vm)root@debian:~# nano /etc/environment
    ```

        HTTP_PROXY=http://cache.univ-lille.fr:3128
        HTTPS_PROXY=http://cache.univ-lille.fr:3128
        http_proxy=http://cache.univ-lille.fr:3128
        https_proxy=http://cache.univ-lille.fr:3128
        NO_PROXY=localhost,192.168.194.0/24,172.18.48.0/22
    
```bash
(vm)root@debian:~# reboot
```

!!! Success Accès internet
    ```bash
    (vm)root@debian:~# wget https://www.framasoft.org
    ```

    Maintenant ça fonctionne !

#### 2.4.2.3 Mises à jour

!!! note Mises a jour
    ```bash
    (vm)root@debian: apt update && apt full-upgrade
    ```

#### 2.4.2.4 Installation d’outils

!!! note Installation de vim

    ```bash
    (vm)root@debian: apt-get install vim
    ```

!!! tip Lien de la prochaine procédure

    [Mettre en place des alias](./5_ssh_config_alias.md)