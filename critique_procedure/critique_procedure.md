# Critique d'une procédure SAE 3.03

!!! abstract Groupe J
    Renan Declercq
    Clément Delerue

!!! abstract Repo Gitlab
    - <https://gitlab.univ-lille.fr/utilisateur/sae-deploiement.git>

!!! danger Critique de la procédure ci-dessous
    - Problème de formatage
    - Non respect des conventions pour indiquer QUI / QUOI / OU ? Aucun contexte
    - Manque des étapes
    - Imprécision sur les commandes
    - Des commandes qui ne fonctionnent pas
    - Pas d'explication sur l'objectif des étapes
    - Incomplet

## 1. Machine Physique

    ssh xxxxx.iutinfo.fr

vérifier la clé d’authenticité

    ssh-keygen nom_de_fichier passphrase

ssh-copy-id -i $HOME/.ssh/id_rsa.pub login@xxxxx

    rentrer la passphrase quand demandé

source /home/public/vm/vm.env

    creer la machine virtuelle

vmiut creer matrix

    Voir toutes vos vm

vmiut lister

    démarrer la machine

vmiut demarrer matrix

    obtenir des informations sur la machine

vmiut info

    se déconnecter de la vm

exit

    se connecter a la machine de virtualisation

    ssh -X xxxxx
    ssh login@192.168.194.(ce qui a été trouvé à l’utilisation de la commande vmiut info)

    utiliser la machine virtuelle

vmiut console matrix

    se connecter en tant que root

login root

mdp root

## 2. Machine Virtuelle

    couper l’interface réseau

ifdown enp0s3

    modifier les fichiers

    /etc/network/interfaces

ajout de iface enp0s3 inet static address 192.168.194.3/24 gateway 192.168.194.2

    /etc/resolv.conf

ne rien toucher

    activer l’interface réseau

ifup enp0s3

    tester si la configuration a été sauvegardée

reboot

    changer d’utilisateur

    su - user
    su - root

    modifier le fichier pour les proxy:

    /etc/environment

HTTP_PROXY=http://cache.univ-lille.fr:3128

HTTPS_PROXY=http://cache.univ-lille.fr:3128

http_proxy=http://cache.univ-lille.fr:3128

https_proxy=http://cache.univ-lille.fr:3128

NO_PROXY=localhost,192.168.194.0/24,172.18.48.0/22

    passer sous user et utiliser la commande

wget https://www.framasoft.org

    passer sous root et utiliser la commande

apt update && apt full-upgrade

cocher la case [ ] /dev/sda

    reboot ## Machine virtuelle

    modifier les fichiers

    /etc/hostname
    /etc/hosts remplacer debian par matrix

    installer sudo

apt install sudo

    donner des droits sur sudo pour user

adduser user sudo

Nécessite l’appel de la commande sudo avant toute utilisation de commande root sous user

    paramétrer la date

modifier /etc/systemd/timesyncd.conf

NTP= ntp.univ-lille.fr

systemctl restart systemd-timesyncd

    installer postgesql

apt install postgresql

    vérifier si postgresql est actif

systemctl | grep postgresql

    créer un utilisateur

su - postgres

createuser -P matrix

    créer base de données

owner: matrix, nom: matrix

createdb -O matrix matrix