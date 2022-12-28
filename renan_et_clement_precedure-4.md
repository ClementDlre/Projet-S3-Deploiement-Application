# Semaine 4 : Element web et reverse proxy

## Installation d'Element

### Choix d'un serveur web

Là il faut justifier pourquoi on a choisi l'un ou l'autre.  
Idées:

- nginx j'ai déjà utilisé donc c'est plus rapide

fin de l'argumentation

### Installation et configuration de nginx

nginx est déjà installé sur la machine virtuelle, il ne reste donc plus qu'à le configurer pour pouvoir accéder à Element.

Les fichiers de configuration de nginx se trouve à `/etc/nginx`. Dans le dossier `sites-available`, créer un nouveau fichier `element` et le remplir :

```
server {
    listen 8080;

    location / {
        root /var/www/element;
    }
}
```

Cette configuration crée un simple serveur qui écoute le port 8080 et redirige toutes les requêtes vers le système de fichier à `/var/www/element`. Par exemple, une requête à `localhost/index.html:8080` renverra le fichier `/var/www/element/index.html`.

Pour que cette configuration fonctionne, il faut que ce fichier soit accessible dans le dossier `sites-enabled`. La manière standard de faire cela avec nginx est de faire un lien symbolique :

```console
user@matrix:/etc/nginx/sites-enabled$ sudo unlink default
user@matrix:/etc/nginx/sites-enabled$ sudo ln -s /etc/nginx/sites-available/element /etc/nginx/sites-enabled/element
```

On en profite pour retirer le lien symbolique vers default qui ne nous servira plus.

### Configuration de Element

On doit maintenant fournir les fichiers statiques de Element pour les rendre accessible.

On doit, dans l'ordre, créer le dossier précisé avec `root /var/www/element` précédemment, puis on télécharge l'archive de la dernière version d'Element, on désarchive le fichier téléchargé et on s'assure que tous les fichiers soient disponibles dans le répertoire.

```
user@matrix:/$ sudo mkdir -p /var/www/element
user@matrix:/$ cd /var/www/element
user@matrix:/var/www/element$ sudo -E wget https://github.com/vector-im/element-web/releases/download/v1.11.15/element-v1.11.15.tar.gz
[sudo] password for user: 
--2022-12-07 09:48:16--  https://github.com/vector-im/element-web/releases/download/v1.11.15/element-v1.11.15.tar.gz
Proxy request sent, awaiting response... 302 Found

[...]

Proxy request sent, awaiting response... 200 OK
Length: 19284416 (18M) [application/octet-stream]
Saving to: ‘element-v1.11.15.tar.gz’

element-v1.11.15.ta 100%[===================>]  18,39M  94,7MB/s    in 0,2s    

2022-12-07 09:48:17 (94,7 MB/s) - ‘element-v1.11.15.tar.gz’ saved [19284416/19284416]

user@matrix:/var/www/element$ tar -xf element-v1.11.15.tar.gz

user@matrix:/var/www/element$ sudo -E cp ./element-v1.11.15/* .

user@matrix:/var/www/element$ rmdir element-v1.11.15
user@matrix:/var/www/element$ rm element-v1.11.15.tar.gz
```

Après ces commandes, on doit avoir tous les fichiers de l'archive directement dans `/var/www/element`, là où nginx ira chercher les fichiers

```
user@matrix:/var/www/element$ ls
0eb45cba2c9fe4094df0.worker.js      contribute.json         jitsi_external_api.min.js  opus-decoderWorker.min.5ddc340.js  usercontent
0eb45cba2c9fe4094df0.worker.js.map  decoder-ring            jitsi.html                 opus-encoderWorker.min.aeff202.js  vector-icons
47c1cbac7a251a50351f.worker.js      decoderWorker.min.wasm  manifest.json              recorder-worklet.674feaf.js        version
47c1cbac7a251a50351f.worker.js.map  fonts                   media                      recorder-worklet.674feaf.js.map    wave-encoderWorker.min.945cec9.js
apple-app-site-association          i18n                    mobile_guide               static                             welcome
bundles                             img                     olm.ed87e3f.wasm           sw.js                              welcome.html
config.json                         index.html              olm_legacy.js              themes
```

Il ne reste plus qu'à renommer le fichier `config.example.json` en `config.json` avec `mv config.example.json` et Element sera prêt à être utilisé.

> On n'a pas besoin de modifier la configuration d'Element car celle de base est suffisante pour notre utilisation. Un simple renommage est donc la seule chose à faire.

### Dernières configurations et test

Pour qu'Element fonctionne correctement, il faut modifier la configuration de notre serveur web comme indiqué dans la documentation. Le fichier `/etc/nginx/sites-available/element` devra maintenant ressembler à cela :

```
server {
    location / {
        root /var/www/element;
    }

    add_header X-Frame-Options SAMEORIGIN;
    add_header X-Content-Type-Options nosniff;
    add_header X-XSS-Protection "1; mode=block";
    add_header Content-Security-Policy "frame-ancestors 'none'";
}
```

Enfin, pour pouvoir accéder à notre installation depuis la machine physique, il faut rediriger les ports avec SSH. Modifier le fichier de configuration comme ceci : 

```
AddKeysToAgent=confirm
ForwardAgent=yes

Host=virt
    hostname=frene02.iutinfo.fr
    ForwardX11=yes

Host=vm
    user=user
    hostname=192.168.194.3
    LocalForward=0.0.0.0:9090 localhost:8080
    # virt:9090 -> vm:8080 -> element
    LocalForward=0.0.0.0:8008 localhost:8008
    # virt:8008 -> vm:8008 -> synapse

Host=vmjump
    user=user
    hostname=192.168.194.3
    ProxyJump=virt
    LocalForward=0.0.0.0:9090 localhost:8080
    LocalForward=0.0.0.0:8008 localhost:8008
```

Element est accessible depuis http://frene02.iutinfo.fr:9090

![element qui marche bien sur firefox](./nocap.png)

## Reverse proxy pour Synapse

### Choix du reverse proxy

...

### Installation de nginx comme reverse proxy

Premièrement, réinstallation d'une VM en suivant les 3 précédentes procédures jusqu'à l'installation de nginx, mais en passant l'installation de Synapse et Postgres.

Dans `/etc/nginx/sites-available`, créer un nouveau fichier de configuration `reverse-proxy` et créer un reverse proxy simple, en suivant la documentation de nginx et celle de Synapse :

```
server {
    listen 80;
    location / {
        proxy_pass http://192.168.194.3:8008;
        proxy_set_header X-Forwarded-For $remote_addr;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header Host $host;

        client_max_body_size 50M;
        proxy_http_version 1.1; 
    }
}
```

Cette configuration permet des trucs.