# Compte rendu d’installation et vérification du système

Ce document présente les réponses aux questions et points clés associés à l’installation et à la vérification d’un serveur Debian, ainsi que les différentes commandes utilisées, accompagnées d’explications et des résultats.

## 1. Téléchargement des fichiers d’installation :
Le fichier ISO pour télécharger l’installateur Debian se trouve sur le lien suivant :
- **Lien de téléchargement** : https://ftp.lip6.fr/pub/linux/distributions/debian/dists/stable/main/installer-amd64/current/images/netboot/mini.iso

## 2. Installation de la machine virtuelle :
J'ai dû réinstaller la machine virtuelle à trois reprises pour obtenir un environnement de travail optimal. La première tentative a échoué en raison d'un espace de stockage insuffisant. Lors de la deuxième installation, des paquets avec interface graphique ont été installés par erreur, ce qui n'était pas nécessaire pour le projet. Enfin, la troisième installation a été réussie, avec la configuration adéquate et les ressources nécessaires pour le bon fonctionnement de l'environnement.

## 3. Installation de SSH :
Les commandes utilisées pour installer et configurer le serveur SSH :
- `apt search ssh` : Recherche du paquet SSH.
- `apt install openssh-server` : Installation du serveur SSH.
- `nano /etc/ssh/sshd_config` : Modification du fichier de configuration SSH.
    - Changement de la ligne `PermitRootLogin` en `yes` et suppression du commentaire `#` pour autoriser la connexion en tant que root.
- `systemctl restart ssh` : Redémarrage du service SSH.

**Commande connexion SSH** : `ssh root@127.0.0.1`

Ces commandes permettent l’installation du serveur SSH, la configuration du fichier pour autoriser l’accès root, puis le redémarrage et la vérification du statut du service SSH. La connexion est ensuite testée en local sur l’adresse 127.0.0.1.

Pour établir une connexion SSH avec une machine hôte, il est nécessaire de configurer la machine virtuelle :
- Activer la redirection du port 22 (port invité) vers le port 2222 (port hôte).
- Redémarrer la machine virtuelle.
- Utiliser la commande suivante pour se connecter : `ssh root@127.0.0.1 -p 2222`

## 4. Commandes d’information système :
- `dpkg -l | wc -l` : 352 paquets sont installés sur le système.
- `df -h` : Affiche l’espace disque utilisé et disponible. Ici, on remarque que 1,2 Go sont utilisés par la partition racine.
- `echo $LANG` : Indique la langue du système, ici `fr_FR.UTF-8` (Français).
- `hostname` : Le nom de la machine est `serveur1`.
- `hostname -d` : Le nom de domaine est `urf-info-p6.jussieu.fr`.

Ces commandes fournissent des informations sur l’état du système, telles que les paquets installés, l’espace disque utilisé, la langue configurée et le nom de la machine et du domaine.

## 5. Fichiers de mots de passe et comptes utilisateurs :
### Commande : ` cat /etc/apt/sources.list | grep -v -E '^#|^$'`
Cette commande affiche les dépôts logiciels utilisés par le système APT sans afficher les lignes vides ou les commentaires. Elle permet de connaître les sources des paquets Debian pour la distribution Bookworm.
```
root@serveur1:~# cat /etc/apt/sources.list | grep -v -E '^#|^$'
deb http://deb.debian.org/debian/ bookworm main
deb http://security.debian.org/debian-security bookworm-security main
deb http://deb.debian.org/debian/ bookworm-updates main
```


### Commande : `cat /etc/shadow | grep -vE ':\*:|:!\*:'`
Ce fichier contient les mots de passe chiffrés et les informations d’authentification des utilisateurs du système. Le filtre enlève les comptes sans mot de passe, désactivés ou verrouillés.
```
root@serveur1:~# cat /etc/shadow | grep -vE ':\*:|:!\*:'
root:$y$j9T$jp4OUOPDKs9lDTa3XLslG.$ebchWpfHlvZak.lvlcR.7/8eOP4tz0VTS717gxVBlQ9:19998:0:99999:7:::
messagebus:!:19998::::::
avahi-autoipd:!:19998::::::
sshd:!:19998::::::
```

### Commande : `cat /etc/passwd | grep -vE 'nologin|sync'`
Ce fichier contient des informations sur les comptes utilisateurs. Le filtre exclut les comptes système ou désactivés.


Ces commandes permettent d’obtenir une liste filtrée des comptes actifs sur le système, excluant ceux sans mot de passe ou non interactifs.

## 6. Gestion des partitions :
- `fdisk -l` : Liste les partitions de tous les disques disponibles sur le système.
```
Modèle de disque : VBOX HARDDISK
Unités : secteur de 1 × 512 = 512 octets
Taille de secteur (logique / physique) : 512 octets / 512 octets
taille d'E/S (minimale / optimale) : 512 octets / 512 octets
Type d'étiquette de disque : gpt
Identifiant de disque : C6F46E0F-5DF7-4FD2-ABD7-E9D545FD058D

Périphérique    Début      Fin Secteurs Taille Type
/dev/sda1        2048 19531775 19529728   9,3G Système de fichiers Linux
/dev/sda2    19531776 27344895  7813120   3,7G Système de fichiers Linux
/dev/sda3    27344896 29298687  1953792   954M Système de fichiers Linux
/dev/sda4    29298688 41940991 12642304     6G Partition d'échange Linux
```

- `fdisk -x` : Affiche une version plus détaillée avec le nom et l’UUID de chaque partition.
```
root@serveur1:~# fdisk -x
Disque /dev/sda : 20 GiB, 21474836480 octets, 41943040 secteurs
Modèle de disque : VBOX HARDDISK
Unités : secteur de 1 × 512 = 512 octets
Taille de secteur (logique / physique) : 512 octets / 512 octets
taille d'E/S (minimale / optimale) : 512 octets / 512 octets
Type d'étiquette de disque : gpt
Identifiant de disque: C6F46E0F-5DF7-4FD2-ABD7-E9D545FD058D
Premier LBA utilisable: 34
Dernier LBA utilisable: 41943006
LBA alternatif: 41943039
LBA de départ des entrées de partition: 2
Entrées de partitions allouées: 128
LBA de fin des entrées de partition: 33

Périphérique    Début      Fin Secteurs Type-UUID                            UUID                                 Nom          Attr.
/dev/sda1        2048 19531775 19529728 0FC63DAF-8483-4772-8E79-3D69D8477DE4 F620776F-3B32-4DFE-928F-7C9FA4BF9A8F la racine
/dev/sda2    19531776 27344895  7813120 0FC63DAF-8483-4772-8E79-3D69D8477DE4 FED8E703-9F17-4F16-9735-F489019DE207 espace tempo
/dev/sda3    27344896 29298687  1953792 0FC63DAF-8483-4772-8E79-3D69D8477DE4 C356FD0B-FB61-4EC7-9C5C-AC87AA61F441 les logs
/dev/sda4    29298688 41940991 12642304 0657FD6D-A4AB-43C4-84E5-0933C84B4F4F 2E615512-607D-49BF-AA18-2D5D7C997AEE ma swap
```


Ces commandes sont utiles pour visualiser les partitions présentes sur les disques du système, avec un niveau de détail variable selon l'option utilisée.

## 7. Preseed, Rescue Mode et gestion des partitions :
### Preseed :
Le preseed est utilisé pour automatiser l'installation d'un système d'exploitation, comme Debian ou Ubuntu.

### Mode Rescue :
Le mode rescue permet de réparer un système cassé ou de réinitialiser un mot de passe root oublié.

#### Étapes pour changer le mot de passe root en mode rescue :
1. Démarrer en mode rescue : Sélectionner le mode rescue dans le menu GRUB ou utiliser un live CD/USB.
2. Monter la partition racine : Monter la partition dans un répertoire temporaire comme `/mnt`.
3. Changer le mot de passe : Utiliser `chroot /mnt`, puis la commande `passwd` pour changer le mot de passe root.
4. Redémarrer le système : Quitter le chroot avec `exit` et redémarrer.

### Redimensionnement de la partition racine sans réinstallation :
Le redimensionnement d'une partition racine nécessite généralement un live CD.

#### Étapes pour redimensionner la partition racine :
1. Sauvegarder les données importantes.
2. Démarrer à partir d’un live CD/USB.
3. Utiliser un outil comme GParted pour redimensionner la partition racine.
4. Appliquer les changements et redémarrer le système sans le live CD.
