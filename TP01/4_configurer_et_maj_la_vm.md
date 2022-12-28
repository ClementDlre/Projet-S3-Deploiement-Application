
## 2.4 Configurer et mettre à jour la machine virtuelle

### 2.4.1 Connexion root et SSH

Essayez de vous connecter à la machine virtuelle en SSH sur le compte root.

Quelle commande avez vous utilisée ?

    ```bash
    (virtu)utilisateur@acajou12:~$ ssh root@192.168.194.3
    ```

Que se passe-t’il ?

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

Pourquoi ? Il faut se connecter avec user

```bash
(virtu)utilisateur@acajou12:~$ ssh user@192.168.194.3
```

```bash
user@192.168.194.3's password: user
```

```bash
Linux debian 5.10.0-9-amd64 #1 SMP Debian 5.10.70-1 (2021-09-30) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Thu Nov 17 11:09:18 2022 from 192.168.194.1
```

!!!info Informations
    ```bash
    su -l  : --login ou -l -> Exécute un script de connexion pour passer à un nom d’utilisateur spécifique. Vous devrez entrer un mot de passe pour cet utilisateur.
    ```

```bash
su -l 
password : root
```

### 2.4.2 Accès extérieur pour les VM

#### 2.4.2.1 Un peu de réseau

!!! info Informations
    Pour que vos VM aient accès à l’extérieur de ce réseau, elles passent par le routeur 192.168.194.2. Ce routeur est équivalent à la box internet que vous pouvez avoir chez vous. Il ne fait pas qu’un simple routage mais de la translation d’adresse (ou NAT: Network Address Translation en anglais).

    Le principe du NAT est que le routeur remplace l’adresse des machines qui veulent sortir du réseau par son adresse publique (son adresse qui lui permet, lui, de sortir de son réseau). Quand votre machine virtuelle se connecte sur une machine extérieure au réseau virtuel principal (à une autre machine de salle de TP par exemple), celle ci pensera que c’est le routeur (et donc la machine de virtualisation) qui la contacte directement

```bash
user@vm $ wget https://www.framasoft.org
```

!!! bug Erreur
    Ca ne fonctionne pas encore car il faut configurer le proxy !

#### 2.4.2.2 Configuration du proxy

```bash
nano /etc/environment
```

    HTTP_PROXY=http://cache.univ-lille.fr:3128
    HTTPS_PROXY=http://cache.univ-lille.fr:3128
    http_proxy=http://cache.univ-lille.fr:3128
    https_proxy=http://cache.univ-lille.fr:3128
    NO_PROXY=localhost,192.168.194.0/24,172.18.48.0/22
    
```bash
(vm)root@debian:~# reboot
```

```bash
(vm)root@debian:~# wget https://www.framasoft.org
```

Maintenant ça fonctionne !

#### 2.4.2.3 Mise à jour

```bash
(vm)root@debian: apt update && apt full-upgrade
```

```bash
(vm)root@debian: reboot
```

#### 2.4.2.4 Installation d’outils

```bash
(vm)root@debian: apt-get install vim
```
