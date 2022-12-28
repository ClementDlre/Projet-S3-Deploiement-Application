## 2.2 Connexion à distance

### 2.2.1 Première connexion à la machine de virtualisation

!!!info Informations
    La machine physique = hevea19
    La machine de virtualisation = acajou12

| Fingerprint                                              |       Machine       |      Algo |
| :------------------------------------------------------- | :-----------------: | --------: |
| 3072  SHA256:1pXDryxLNlVsKPTkEIAona46yIf/peCZ88qTFbxvHH4 | acajou12.iutinfo.fr |     (RSA) |
| 256  SHA256:CrB3/QEaXDvn0Qp1dQapmYSejef1Zh45oEl4Ozuk1hU  | acajou12.iutinfo.fr |   (ECDSA) |
| 256  SHA256:4QYLsyCTuLFa2QCk+jqi4MBPuqpO4yD+FULHAcXj7lU  | acajou12.iutinfo.fr | (ED25519) |

```bash
(phy)utilisateur@hevea19:~$ ssh acajou12.iutinfo.fr
```

Effectuer les validations :

```bash
The authenticity of host 'acajou12.iutinfo.fr (172.18.49.39)' can't be established.
ECDSA key fingerprint is SHA256:CrB3/QEaXDvn0Qp1dQapmYSejef1Zh45oEl4Ozuk1hU.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

Votre client SSH ajoutera alors le serveur dans le fichier $HOME/.ssh/known_hosts pour indiquer que vous lui faites confiance.

```bash
yes
```

```bash
utilisateur@acajou12.iutinfo.fr's password: [Entrer le mdp]
```

```bash
Connection closed by 172.18.49.39 port 22
```

!!! info Informations
    Devoir saisir son mot de passe à chaque connexion peut vite s’avérer pénible, surtout si on doit le faire souvent. SSH permet de s’authentifier autrement qu’avec un simple mot de passe.

    Le principe d’utilisation pour l’authentification SSH est le suivant: vous donnez au serveur SSH votre clé publique et, au moment de la connexion, un challenge cryptographique permettra au serveur de vérifier que vous possédez bien la clé privée associée à la clé publique sans que votre clé privée ne soit jamais diffusée en dehors de votre machine.

### 2.2.2 Faciliter la connexion

- Fabriquer une paire de clés
  La commande demande :

  - un nom de fichier : vous pouvez laisser le nom de fichier par défaut. Notez le. Il est bon de le connaître
  - une passphrase: c’est un mot de passe qui permet de chiffrer le fichier contenant votre clé privé. Il est très important d’utiliser un mot de passe pertinent. Ainsi, si on vous vole le fichier, le voleur ne pourra pas se servir de votre clé.

```bash
(phy)utilisateur@hevea19:~$ ssh-keygen
```

```bash
Generating public/private rsa key pair.
Enter file in which to save the key (/home/infoetu/utilisateur/.ssh/id_rsa):
```

Ici, j'entre la passphrase choisie (voir Notes smartphone)

```bash
Enter passphrase (empty for no passphrase):
Enter same passphrase again: 
```

```bash
Your identification has been saved in /home/infoetu/utilisateur/.ssh/id_rsa
Your public key has been saved in /home/infoetu/utilisateur/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:zw56Ut+qEh0QaWg42E7gAJZHNYzztuvGPn0QbnH5Frc utilisateur@hevea19
The key's randomart image is:
+---[RSA 3072]----+
|==oo++.o         |
|=.=+o.=          |
| +.oo. .  .      |
|  .  o o.o . .   |
|    . o.S.. o .  |
|     ..+oo o E   |
|    . +oo.+.     |
|     =oo.+. .    |
|    +oo+o.o.     |
+----[SHA256]-----+
```

- Transmettre la clé publique au serveur.

    Quand un utilisateur tente de se connecter à un serveur SSH, celui-ci consulte le fichier $HOME/.ssh/authorized_keys3 à la recherche de clés publiques autorisées à se connecter pour l’utilisateur

    Ce fichier, dont un exemple est donné ci-dessous, est constitué d’une clé publique par ligne.

        ssh-rsa AAAAB3NzaC1 [...] +1ts5x6ZXE= login@phys
        ssh-rsa AAAAB3NzaC1 [...] o/6cLqTmM8= commentaire
        ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIGHaJOp3Vx34PVVEt6ZzTii60Sd3Hl5CZTiqgx37f8hp user@machine

    !!!info Informations
        Les deux premières lignes on été tronquée pour des raisons de lisibilité. Le format d’une ligne est

            [options] type-clé clé commentaire

  - Solution 1 : le faire manuellement

  - Solution 2 : utiliser la commande ssh-copy-id

        On utilisera les commandes sur la machine de virtualisation

        ```bash
        ssh acajou12.iutinfo.fr
        ```

        ```bash
        man ssh-copy-id
        ```

        ```bash
        ssh-copy-id -i ~/.ssh/id_rsa.pub utilisateur@acajou12
        ```

        ```bash
        /usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/infoetu/utilisateur/.ssh/id_rsa.pub"
        The authenticity of host 'acajou12 (172.18.49.39)' can't be established.
        ECDSA key fingerprint is SHA256:CrB3/QEaXDvn0Qp1dQapmYSejef1Zh45oEl4Ozuk1hU.
        Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
        /usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
        /usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
        utilisateur@acajou12's password: 

        Number of key(s) added: 1

        Now try logging into the machine, with:   "ssh 'utilisateur@acajou12'"
        and check to make sure that only the key(s) you wanted were added.
        ```

    - le client propose au serveur des identifiants de clé avec lesquels il peut s’authentifier (par défaut, la clé présente dans les fichiers id_dsa, id_ecdsa, id_ed25519 et id_rsa) ;
    - si la clé publique est présente dans le fichier authorized_keys, le serveur génère un challenge aléatoire et le transmet au client ;
    - le client renvoie une signature du challenge qu’il effectue avec sa clé privée ;
    - le serveur vérifie la signature à l’aide de la clé publique ;
    - le client est identifié, la connexion est autorisée.

    On reesaye la connexion à la machine de virtualisation :

    ```bash
    utilisateur@hevea19:~$ ssh acajou12.iutinfo.fr
    Linux acajou12 5.10.0-17-amd64 #1 SMP Debian 5.10.136-1 (2022-08-13) x86_64

    The programs included with the Debian GNU/Linux system are free software;
    the exact distribution terms for each program are described in the
    individual files in /usr/share/doc/*/copyright.

    Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
    permitted by applicable law.
    Last login: Thu Nov 17 09:03:29 2022 from 172.18.49.100
    utilisateur@acajou12:~$ 
    ```
