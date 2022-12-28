
## 2 Installation et configuration basique d’un serveur de base de données

### 2.1 Installer postgresql

```bash
(vm)user@matrix:~# apt-get install postgresql
```

!!! note

    `systemd` est un gestionnaire de systèmes d’initialisation et de systèmes qui est devenu la nouvelle norme pour les distributions de Linux.

    `systemctl` est l’outil de gestion essentiel pour contrôler le système d’initialisation

Liste de commandes `systemctl` :

```bash
(vm)user@matrix:~# systemctl start application.service
(vm)user@matrix:~# systemctl stop application.service
(vm)user@matrix:~# systemctl restart application.service
(vm)user@matrix:~# systemctl reload application.service
(vm)user@matrix:~# systemctl reload-or-restart application.service
(vm)user@matrix:~# systemctl enable application.service 
(vm)user@matrix:~# systemctl disable application.service
(vm)user@matrix:~# systemctl status application.service
(vm)user@matrix:~# systemctl is-active application.service
(vm)user@matrix:~# systemctl is-enabled application.service
(vm)user@matrix:~# systemctl is-failed application.service
```

```bash
(vm)user@matrix:~# systemctl status postgresql.service
```

### 2.2 Connexion à postgresql

```bash
(vm)user@matrix:~# sudo -u postgres -i
```

### 2.3 Créer un nouvel utilisateur

```bash
(vm)postgres@matrix:~# createuser -d -r -l -P matrix
Saisir le mot de passe pour le nouveau rôle : matrix
Saisir le mot de passe à nouveau : matrix
```

!!! note
    *-d = --createdb (Permet au nouvel utilisateur de créer une base de données)
    * -r = --createrole (permet au nouvel utilisateur de créer un rôle)
    *-l = --login (Permet au nouvel utilisateur de se connecter , option par défaut )
    * -P = --pwprompt (Permet à l'utilisateur de créer un mot de passe qui sera demandé à la connexion)

### 2.4 Créer une nouvelle base de données

```bash
(vm)postgres@matrix:~# createdb -O matrix matrix
```

!!! note
    * -O = --owner (permet de spécifier le propriétaire de la base de données)

!!! danger Changer d'utilisateur
    Pour les commandes suivantes, executer les commandes avec l'utilisateur user et non pas postgres 

```bash
(vm)user@matrix:~# psql -h localhost -U matrix -d matrix -c '\x' -c 'create table test(id Integer);'
```

!!! note
    * -h = --host (L'endroit où est installé postgres)
    * -U = --username (Nom du compte utilisateur avec lequel on se connecte à la base de données)
    *-d = --dbname (Nom de la base de données)
    * -c = --command ('\x' indique l'execution d'une requête sql)

```bash
(vm)user@matrix:~# psql -h localhost -U matrix -d matrix
matrix => \d
```

### 2.5 Manipuler une base de données

```sql
(vm)postgres@matrix:~# 
matrix => insert into test values(1);
```

```sql
(vm)postgres@matrix:~# 
matrix => select * from test;
```