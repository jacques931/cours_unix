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
![image](https://github.com/user-attachments/assets/a09ea7ca-3a48-4e5d-8555-a5e7bab5cf25)


### Commande : `cat /etc/passwd | grep -vE 'nologin|sync'`
Ce fichier contient des informations sur les comptes utilisateurs. Le filtre exclut les comptes système ou désactivés.


Ces commandes permettent d’obtenir une liste filtrée des comptes actifs sur le système, excluant ceux sans mot de passe ou non interactifs.

## 6. Gestion des partitions :
- `fdisk -l` : Liste les partitions de tous les disques disponibles sur le système.
![image](https://github.com/user-attachments/assets/54a38795-be38-40d6-9138-889da4ccbfcc)

- `fdisk -x` : Affiche une version plus détaillée avec le nom et l’UUID de chaque partition.
![image](https://github.com/user-attachments/assets/5fd5969e-be46-4284-95f2-f028223cf689)


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
