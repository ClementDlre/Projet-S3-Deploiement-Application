
## 2.3 Creer et gérer des machines virtuelles

!!! warning Attention
    Vous devez exécuter toutes les commandes de cette section sur votre machine de virtualisation.

Executer le script vmiut :

```bash
(virtu)utilisateur@acajou12:~$ source /home/public/vm/vm.env
```

!!! warning Attention
    Vous devrez utiliser la commande `source` dans chaque nouveau shell

!!! info Informations
    Pour manipuler les machines virtuelles utilisez vmiut (cf 'vmiut --help').
    Les machines virtuelles seront stockées sur acajou12 dans /usr/local/virtual_machine/infoetu/utilisateur.
    


### 2.3.1 Création d’une machine virtuelle

```bash
(virtu)utilisateur@acajou12:~$ vmiut creer matrix
```

```bash
Virtual machine 'matrix' is created and registered.
UUID: 5c1daebf-bb94-4988-add4-11052b12e2e8
Settings file: '/usr/local/virtual_machine/infoetu/utilisateur/matrix/matrix.vbox'
0%...10%...20%...30%...40%...50%...60%...70%...80%...90%...100%
Clone medium created in format 'VMDK'. UUID: 47653a3f-bda0-41a0-8eb2-6ae1429665e1
# Paramètres vmiut
MACHINE=matrix
VBOXES=/usr/local/virtual_machine/infoetu/utilisateur
RESEAU=vmnet8
MEMOIRE=1024
VRDEPORT=5000-5050
MODELE=/home/public/vm/disque-5Go-bullseye.vdi
HOST=acajou12

# Paramètres VirtualBox
name=matrix
UUID=5c1daebf-bb94-4988-add4-11052b12e2e8
path=/usr/local/virtual_machine/infoetu/utilisateur/matrix
memory=1024
etat=poweroff
vrdeport=-1
mac=08:00:27:f4:d3:c6
```

```bash
(virtu)utilisateur@acajou12:~$ vmiut lister
```

```bash
"Win10" {dc87e177-eb41-457c-9fcc-84c0ca9f38ca}
"matrix" {5c1daebf-bb94-4988-add4-11052b12e2e8}
```

!!! danger Problème possible
    Si, au cours de la SAÉ, le résultat de la commande vmiut lister ressemble à : 
    "`<inaccessible>`" {903447fe-d6ca-4f2f-9272-3f465a026540}

    alors soit:
    - Vous ne vous trouvez pas sur la machine de virtualisation ;
    - Les fichiers de votre machine virtuelle ont été effacés.

### 2.3.2 Démarrer la machine virtuelle

```bash
(virtu)utilisateur@acajou12:~$ vmiut demarrer matrix
```

```bash
Waiting for VM "matrix" to power on...
VM "matrix" has been successfully started.
```

### 2.3.3 Arrêt et suppression de la machine virtuelle

!!! danger Arreter la vm 
    `A faire à la fin de chaque séance `

    ```bash
    (virtu)utilisateur@acajou12:~$ vmiut arreter matrix
    ```

!!! warning Supprimer la vm
    ```bash
    (virtu)utilisateur@acajou12:~$ vmiut supprimer matrix
    ```

!!! tip Astuce
    Si vous souhaitez les sauvegarder, vous pouvez copier le répertoire /usr/local/virtual_machine/login/`NOMDEMACHNE`

### 2.3.4 Obtenir des informations sur la machine virtuelle

```bash
(virtu)utilisateur@acajou12:~$ vmiut info matrix
```

```bash
# Paramètres vmiut
MACHINE=matrix
VBOXES=/usr/local/virtual_machine/infoetu/utilisateur
RESEAU=vmnet8
MEMOIRE=1024
VRDEPORT=5000-5050
MODELE=/home/public/vm/disque-5Go-bullseye.vdi
HOST=acajou12

# Paramètres VirtualBox
name=matrix
UUID=5c1daebf-bb94-4988-add4-11052b12e2e8
path=/usr/local/virtual_machine/infoetu/utilisateur/matrix
memory=1024
etat=running
vrdeport=5000
mac=08:00:27:f4:d3:c6
ip-possible=192.168.194.25
```

!!! info Informations
    Si ip-possible= ""
    -> Il faut attendre quelques minutes et relancer la commande 

### 2.3.5 Quelques informations sur le réseau et la VM

|                     Machine                      |                        Adresse |
| :----------------------------------------------: | -----------------------------: |
|            Machine de virtualisation             |                  192.168.194.1 |
|                   Routeur, DNS                   |                  192.168.194.2 |
| Adresses dynamiques (attribuées automatiquement) | 192.168.194.25-192.168.194.128 |

!!!info Informations
    La machine virtuelle a été créée à partir d’un modèle.

    Voici les caractéristiques du modèle:

    Distribution: Debian GNU/Linux 11 (bullseye)
    Utilisateur standard: user, mot de passe: user
    Administrateur: root, mot de passe: root
    empreinte de la clé SSH: SHA256:SUHhxVJVZFiBQ6/koNbZfA9reKHyzIrvPgJvOEJ8zuE

### 2.3.6 Utilisation de la machine virtuelle

- Solution 1 : Utiliser une console virtuelle: simule un clavier et un écran qui serait connectés physiquement à la machine virtuelle
- Solution 2 : Se connecter en SSH.

#### 2.3.6.1 Solution 1 : Console virtuelle

```bash
(virtu)utilisateur@acajou12:~$ vmiut console matrix
```

!!! bug Failed to open display
    ```bash
    ERROR: Failed to open display:
    ```

    !!! info Informations
        Ceci est du au fait que la console virtuelle est une application graphique mais que vous êtes connecté à distance sur la machine de virtualisation. L’application graphique ne peut donc pas afficher sa fenêtre.

!!!tip Solution
    Pour palier à ce problème, nous allons utiliser une fonctionnalité de SSH qui permet de rediriger une application graphique par la connexion SSH. Pour cela, déconnectez vous de la machine de virtualisation et reconnectez vous avec la commande suivante :

    ```bash
    (virtu)utilisateur@acajou12:~$ vmiut arreter matrix
    ```

    Sortir de la machine de virtualisation 
    ```bash
    (virtu)utilisateur@acajou12:~$ CTRL + D 
    ```


    ```bash
    (phy)utilisateur@hevea19:~$ ssh -X acajou12.iutinfo.fr
    ```

!!! info Informations
    l’option -X de SSH permet d’effectuer la redirection graphique. Vous pouvez maintenant retenter la commande vmiut console matrix.

!!!warning Ne pas oublier
    ```bash
    (virtu)utilisateur@acajou12:~$ source /home/public/vm/vm.env
    ```

```bash
(virtu)utilisateur@acajou12:~$ vmiut demarrer matrix
```

```bash
(virtu)utilisateur@acajou12:~$ vmiut console matrix
```

Connectez vous en tant que root et utilisez les commandes ip addr show et ip route show pour constater que les paramètres de réseau correspondent bien à ce qui est attendu.

```bash
vm$ debian login : root
password : root
```

```bash
vm$ ip addr show
```

<img src="../images/ip_addr.png">

```bash
vm$ ip route
```

<img src="../images/ip_route.png">

#### 2.3.6.2 Solution 2 : Connexion SSH

```bash
(virtu)utilisateur@acajou12:~$ ssh user@192.168.194.25
```

```bash
The authenticity of host '192.168.194.25 (192.168.194.25)' can't be established.
ECDSA key fingerprint is SHA256:SUHhxVJVZFiBQ6/koNbZfA9reKHyzIrvPgJvOEJ8zuE.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.194.25' (ECDSA) to the list of known hosts.

user@192.168.194.25's password: user
```

### 2.3.7 Changement de la configuration réseau

Votre machine virtuelle sera un serveur, hébergeant un service. Il est préférable qu’elle ait donc toujours la même adresse IP. Nous allons choisir l’adresse 192.168.194.3

```bash
(virtu)utilisateur@acajou12:~$ CTRL + D
```

```bash
(virtu)utilisateur@acajou12:~$ vmiut console matrix
```

```bash
root@vm#  ifdown enp0s3
```

<img src="../images/ifdown.png">

!!!note Modifier les fichier interfaces et resolv.conf
    Modifiez les fichiers /etc/network/interfaces et /etc/resolv.conf de façon à ce que la VM ait l’adresse statique 192.168.194.3 et qu’elle utilise le routeur 192.168.194.2 et serveur DNS 192.168.194.2.

    Adresse statique et routeur :

    ```bash
    root@vm# nano /etc/network/interfaces
    ```

    <img src="../images/interfaces.png">

    !!! Info Informations Le fichier resolv.conv est déjà bien configuré
        DNS (nameserver):

        ```bash
        root@vm# nano /etc/resolv.conf
        ```

        <img src="../images/resolv.png">

    Redémarrer l'interface reseau

    ```bash
    root@vm# ifup enp0s3
    ```

<img src="../images/ip_addr_post_config.png">

<img src="../images/ip_route_post_config.png">

Utilisez la commande suivante pour redémarrer la machine virtuelle et vérifier que la configuration réseau est bien persistante

```bash
root@vm# reboot
```