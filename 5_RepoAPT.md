# Gestion de apt

## Commandes de base
`apt update` : Met à jour la cache de dépôts

`apt upgrade` : Met à jour les paquets installés

`apt list --installed` : Liste les paquets installés

`apt install <pkgname>` : Installe un paquet et ses dépendances

`apt remove <pkgname>` : Désinstalle un paquet.

## Gestion des versions

### Lister les versions disponibles d'un package
`apt list --all-versions <pkgname>` : Liste toutes les versions disponibles dans les dépôts connus.
### Installer une version spécifique d'un package
`apt install <pkgname>=<version>`
### 'Geler' la version d'un package
`apt-mark hold <pkgname>` : Gèle un paquet. Il ne sera pas mis à lors d'un `apt upgrade`
`apt-mark unhold <pkgname>`: Dégèle un paquet.

## Gérer les dépôts

### /etc/apt/sources.list
'Ancienne' façon de configurer les points de dépôt. 

### /etc/apt/sources.list.d
Répertoire dans lequel se trouvent les fichiers de définissions des points de dépôt

### Exemple d'une configuration moderne
Dans le fichier `/etc/apt/sources.list.d/trixie.sources` :
```
Types: deb deb-src
URIs: http://deb.debian.org/debian
Suites: trixie trixie-updates
Components: main non-free-firmware
Signed-By: /usr/share/keyrings/debian-archive-keyring.gpg
```

Ce serait la configuration pour les dépôts relatif à Debian 13 (trixie)é

Pour les dépôts de la version 12 (bookwork), on pourrait créer le fichier `/etc/apt/sources.list.d/bookworm.sources` contenant : 

```
Types: deb deb-src
URIs: http://deb.debian.org/debian
Suites: bookworm bookworm-updates
Components: main non-free-firmware
Signed-By: /usr/share/keyrings/debian-archive-keyring.gpg
```

Lorsque l'on modifie les définitions de points de dépôts, nous devons rouler la commande `apt update`.
