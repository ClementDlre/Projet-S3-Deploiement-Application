## - Les alias -

!!! note Les alias de toutes nos machines virtuelles

    ```bash
    (virtu)utilisateur@acajou12:~$ nano .ssh/config
    ```

        AddKeysToAgent=confirm

        Host virt
            HostName acajou12.iutinfo.fr
            ForwardAgent yes

        Host matrix
            HostName 192.168.194.3
            ForwardAgent yes
            User user

        Host matrix2jump
            User user
            HostName 192.168.194.3
            ProxyJump virt

        Host rproxy
            HostName 192.168.194.4
            ForwardAgent yes
            User user
            LocalForward 0.0.0.0:9090 localhost:9090
            GatewayPorts yes

        Host db
            HostName 192.168.194.5
            ForwardAgent yes
            User user

        Host element
            HostName 192.168.194.6
            ForwardAgent yes
            User user


!!! tip Lien vers l'architecture finale : conclusion

    [Architecture finale](./1_architecture_finale.md)