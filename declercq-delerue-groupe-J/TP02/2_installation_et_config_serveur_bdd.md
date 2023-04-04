
## - Installation et configuration basique d’un serveur de base de données -

### Installer postgresql

!!! note Installation de postgresql

    ```bash
    (vm)root@matrix:~# apt-get install postgresql
    ```

!!! info Informations

    `systemd` est un gestionnaire de systèmes d’initialisation et de systèmes qui est devenu la nouvelle norme pour les distributions de Linux.

    `systemctl` est l’outil de gestion essentiel pour contrôler le système d’initialisation

    - Liste de commandes `systemctl` :

        ```bash
        (vm)root@matrix:~# systemctl start application.service
        (vm)root@matrix:~# systemctl stop application.service
        (vm)root@matrix:~# systemctl restart application.service
        (vm)root@matrix:~# systemctl reload application.service
        (vm)root@matrix:~# systemctl reload-or-restart application.service
        (vm)root@matrix:~# systemctl enable application.service 
        (vm)root@matrix:~# systemctl disable application.service
        (vm)root@matrix:~# systemctl status application.service
        (vm)root@matrix:~# systemctl is-active application.service
        (vm)root@matrix:~# systemctl is-enabled application.service
        (vm)root@matrix:~# systemctl is-failed application.service
        ```

!!! Success Vérification postgresql.service : active
    ```bash
    (vm)root@matrix:~# systemctl status postgresql.service
    ```

### Quelques manipulations liées à la base de donnée 

!!! note Connexion à postgresql
    
    ```bash
    (vm)root@matrix:~# sudo -u postgres -i
    ```

!!! note Création d'un nouvel utilisateur

    ```bash
    (vm)postgres@matrix:~# createuser -d -r -l -P matrix
    Saisir le mot de passe pour le nouveau rôle : matrix
    Saisir le mot de passe à nouveau : matrix
    ```

    !!! info Informations
        
        * -d = --createdb (Permet au nouvel utilisateur de créer une base de données)
        * -r = --createrole (permet au nouvel utilisateur de créer un rôle)
        *-l = --login (Permet au nouvel utilisateur de se connecter , option par défaut )
        * -P = --pwprompt (Permet à l'utilisateur de créer un mot de passe qui sera demandé à la connexion)


!!! note Création d'une nouvelle base de données
    ```bash
    (vm)postgres@matrix:~# createdb -O matrix matrix
    ```

    !!! info Informations
        * -O = --owner (permet de spécifier le propriétaire de la base de données)

!!! danger Changer d'utilisateur 
    Pour les commandes suivantes, executer les commandes avec l'utilisateur **user** et non pas **postgres**  
        
    ``CTRL + D`` pour revenir à l'utilisateur **user**

!!! note Création d'une table test dans la base de donnée matrix
    
    ```bash
    (vm)user@matrix:~# psql -h localhost -U matrix -d matrix -c '\x' -c 'create table test(id Integer);'
    ```

    !!! info Informations
        
        * -h = --host (L'endroit où est installé postgres)
        * -U = --username (Nom du compte utilisateur avec lequel on se connecte à la base de données)
        *-d = --dbname (Nom de la base de données)
        * -c = --command ('\x' indique l'execution d'une requête sql)

!!! note Se connecter à la base de donnée matrix avec user
    
    ```bash
    (vm)user@matrix:~# psql -h localhost -U matrix -d matrix
    matrix => \d
    ```

### 2.5 Manipuler une base de données

!!! note Insert
    
    ```sql
    (vm)postgres@matrix:~# 
    matrix => insert into test values(1);
    ```

!!! note Select
    
    ```sql
    (vm)postgres@matrix:~# 
    matrix => select * from test;
    ```

!!! tip Lien de la prochaine procédure

    !!! info Informations
        On passe au TP n°3

    [Accès à un service http sur la machine virtuelle](../TP03/1_accès_a_un_service_http_vm.md)