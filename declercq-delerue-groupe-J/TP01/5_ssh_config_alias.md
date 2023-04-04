
## - Les alias configuration SSH -

!!! note Modifier le fichier config

    ```bash
    (phy)utilisateur@hevea19:~$ nano .ssh/config
    ```

        Host virt
        HostName acajou12.iutinfo.fr
        ForwardAgent yes

        Host vm
            HostName 192.168.194.3
            ForwardAgent yes
            User user

        Host vmjump
            User user
            HostName 192.168.194.3
            ProxyJump virt


!!! info Informations 
    Les alias :
    - `ssh virt` : se connecte sur la machine de virtualisation acajou12.iutinfo.fr à partir de la machine physique
    - ``ssh vm`` : Se connecte sur la machine virtuelle matrix avec l'utilisateur user à partir de la machine de virtualisation
    - ``ssh vmjump`` : Se connecte sur la machine virtuelle matrix à partir de la machine physique
    
    !!! warning Attention
        Il faut penser à démarrer la machine matrix avec `vmiut demarrer matrix` avant de se connecter. Par conséquent le ``ssh vmjump`` ne fonctionnera pas si vous ne vous êtes pas encore connecté à la machine de virtualisation pour démarrer matrix au préalable !
    
    !!! info Informations
    
        `ForwardAgent yes` : transfert de votre agent SSH quand vous vous connectez sur la machine de virtualisation

!!! tip Lien de la prochaine procédure

    !!! info Informations
        
        On passe au TP n°2

    [Denière configuration de la machine virtuelle](../TP02/1_derniere_config_vm.md)