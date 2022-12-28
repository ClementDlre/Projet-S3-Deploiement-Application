
## 2.5 Les alias configuration SSH

```bash
(phy)utilisateur@hevea19:~$ codium $HOME/.ssh/config
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

```bash
(phy)utilisateur@hevea19:~$  ssh virt
```

```bash
(phy)utilisateur@hevea19:~$  : ssh vmjump
```

    - alias virt = utilise acajou12.iutinfo.fr
    - alias vm : utilise user par defaut
    - alias vmjump = la machine de virtualisation puis, automatiquement à la VM, sans autre manipulation de votre part -> de la machine physique à la vm directement
    - ForwardAgent yes : transfert de votre agent SSH quand vous vous connectez sur la machine de virtualisation