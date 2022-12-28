## 2.1 Un peu de vocabulaire et de convention

!!!info Informations
    Si nécessaire, le prompt indiquera également précisemment l’utilisateur et la machine sur laquelle la commande doit s’exécuter sous la forme utilisateur@machine. Concernant la machine, il s’agira soit:

    - de phy pour représenter la machine physique
    - de virtu pour représenter la machine de virtualisation ;
    - de vm pour représenter la machine virtuelle dans le cas où il n’y a pas d’ambiguïté ;
    - d’un nom de machine virtuelle s’il est nécessaire de le préciser.

    Dans le cas du nom d’utilisateur, le seul cas particulier sera login. Ce cas se produira pour les commandes à saisir sur la machine physique ou la machine de virtualisation et il voudra simplement dire que vous devez exécuter la commande en utilisant votre compte étudiant.

    Par exemple, si la commande ls est à exécuter sur la machine physique:

    ```bash
    (phy)utilisateur@hevea19:~$ ls
    ```