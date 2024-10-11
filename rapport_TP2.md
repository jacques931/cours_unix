 # Compte rendu de services et processus signaux
## 1/ Secure Shell : SSH
1.1 /
Dans le fichier de configuration SSH (**/etc/ssh/sshd_config**), j’ai modifié la directive PermitRootLogin en yes et retiré le commentaire # pour autoriser les connexions en tant que root. Voici les différentes options possibles :
- **PermitRootLogin yes** : Permet les connexions root. Avantage : accès rapide. Inconvénient : risque de sécurité élevé. À utiliser en dernier recours.
- **PermitRootLogin no** : Interdit les connexions root. Avantage : meilleure sécurité. Inconvénient : accès moins direct. Recommandé en production.
- **PermitRootLogin prohibit-password** : Autorise les connexions root via clés SSH, mais interdit l’authentification par mot de passe. Avantage : sécurisé avec des clés SSH. Inconvénient : gestion des clés nécessaire. Bon compromis.
- **PermitRootLogin forced-commands-only** : Autorise root uniquement pour des commandes spécifiques. Avantage : contrôle strict. Inconvénient : usage limité. Utilisé pour l’automatisation.

1.2/
J’ai généré une paire de clés publique/privée sous Windows en utilisant Git Bash avec l’algorithme RSA et une taille de clé de 4096 bits via la commande `ssh-keygen -t rsa -b 4096`. La clé privée est automatiquement sauvegardée dans le dossier **.ssh** de l’utilisateur. Cependant, en production, il est recommandé d’ajouter une passphrase à la clé privée pour renforcer la sécurité. Sans passphrase, la clé est vulnérable en cas de compromission. 

1.3/
Avec Git Bash, j’ai copié la clé publique vers un serveur virtuel en spécifiant le fichier de clé publique à utiliser avec la commande `ssh-copy-id -i ~/.ssh/id_rsa.pub -p 2222 root@127.0.0.1 `. Cela crée le répertoire **.ssh** et le fichier **authorized_keys** sur le serveur. J’ai vérifié l’existence du répertoire et la présence de la clé avec la commande `cat /root/.ssh/authorized_keys` , puis ajusté les permissions avec `chmod 700 /root/.ssh/authorized_keys ` et verifié avec`ls -l ~/.ssh/authorized_keys`

1.4/ 
Pour se connecter au serveur en utilisant la clé privée, j’ai utilisé la commande `ssh -i ~/.ssh/id_rsa root@127.0.0.1 -p 2222`. Il est essentiel d’avoir préalablement enregistré la clé publique du client dans le fichier authorized_keys du serveur. 

1.5/
Pour sécuriser l'accès SSH à une machine en permettant uniquement la connexion via des clés SSH pour l'utilisateur root et en désactivant l'authentification par mot de passe en modifiant PasswordAuthentication yes  en  PasswordAuthentication no 

Une attaque par brute-force SSH consiste à deviner les identifiants en testant de nombreuses combinaisons de mots de passe. Pour se protéger, on peut utiliser des clés SSH, changer le port par défaut, limiter les connexions par adresse IP, désactiver l'accès SSH pour root, et configurer une limite de tentatives de connexion. Ces mesures rendent les attaques par brute-force beaucoup plus difficiles à réussir. 

## 2/ Processus
2.1/
J’ai utilisé la commande `ps -e -o user,pid,%cpu %mem, stat, start, time, command` pour afficher tous les processus en cours avec des informations détaillées telles que l’utilisateur, le PID, l’utilisation CPU/mémoire, le statut, l’heure de démarrage, le temps CPU total et la commande associée 
```
USER         PID %CPU %MEM STAT  STARTED     TIME COMMAND
root           1  0.0  0.1 Ss   13:54:54 00:00:01 /sbin/init
root           2  0.0  0.0 S    13:54:54 00:00:00 [kthreadd]
root           3  0.0  0.0 I<   13:54:54 00:00:00 [rcu_gp]
root           4  0.0  0.0 I<   13:54:54 00:00:00 [rcu_par_gp]
root           5  0.0  0.0 I<   13:54:54 00:00:00 [slub_flushwq]
root           6  0.0  0.0 I<   13:54:54 00:00:00 [netns]
root          10  0.0  0.0 I<   13:54:54 00:00:00 [mm_percpu_wq]
root         363  0.0  0.0 S    13:55:01 00:00:00 [jbd2/sda2-8]
root         365  0.0  0.0 I<   13:55:01 00:00:00 [ext4-rsv-conver]
root         367  0.0  0.0 S    13:55:01 00:00:00 [jbd2/sda3-8]
root         368  0.0  0.0 I<   13:55:01 00:00:00 [ext4-rsv-conver]
systemd+     405  0.0  0.0 Ssl  13:55:01 00:00:00 /lib/systemd/systemd-timesyncd
root         417  0.0  0.0 Ss   13:55:01 00:00:00 dhclient -4 -v -i -pf /run/dhclient.enp0s3.pid -lf /var/lib/dhcp/dhcli
root         437  0.0  0.0 Ss   13:55:01 00:00:00 /usr/sbin/cron -f
message+     441  0.0  0.0 Ss   13:55:01 00:00:00 /usr/bin/dbus-daemon --system --address=systemd: --nofork --nopidfile
root         443  0.0  0.0 Ss   13:55:01 00:00:00 /lib/systemd/systemd-logind
root         465  0.0  0.0 Ss   13:55:01 00:00:00 /sbin/wpa_supplicant -u -s -O DIR=/run/wpa_supplicant GROUP=netdev
root         492  0.0  0.0 Ss   13:55:06 00:00:00 /lib/systemd/systemd --user
root         493  0.0  0.0 S    13:55:06 00:00:00 (sd-pam)
root         568  0.0  0.0 I    14:13:07 00:00:00 [kworker/0:2-cgwb_release]
root         712  0.0  0.0 I    14:38:23 00:00:00 [kworker/u6:2-events_unbound]
root         718  0.0  0.0 Ss   14:41:02 00:00:00 sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups
root         723  0.0  0.0 I    14:46:33 00:00:00 [kworker/0:1-events]
root         735  0.0  0.0 Ss   14:47:16 00:00:00 /bin/login -p --
root         736  0.0  0.0 I    14:47:16 00:00:00 [kworker/1:2-cgroup_destroy]
root         743  0.0  0.0 S+   14:47:18 00:00:00 -bash
root         767  0.0  0.0 Ss   14:49:29 00:00:00 sshd: root@pts/0
root         773  0.0  0.0 Ss   14:49:29 00:00:00 -bash
root         777  0.0  0.0 I    14:54:15 00:00:00 [kworker/2:0-events]
root         800  0.0  0.0 I    14:56:44 00:00:00 [kworker/u6:0-events_unbound]
root         805  0.0  0.0 I    14:59:26 00:00:00 [kworker/2:1-ata_sff]
root         814  0.0  0.0 I    15:04:38 00:00:00 [kworker/2:2-ata_sff]
root         815  0.0  0.0 R+   15:05:51 00:00:00 ps -eo user,pid,%cpu,%mem,stat,start,time,command
```
L’information Time correspond au  temps total de CPU utilisé par le processus depuis son lancement. Il s'agit de la somme du temps de l'utilisateur et du temps système
Le processus consommant le plus de CPU était `kworker/1:0-events`, et le premier processus lancé après le démarrage du système était `/sbin/init`. 

- `cat /proc/stat | grep processes` permet de savoir le  le nombre approximatif de processus créees depuis le démarrage : `processes 815`
- `uptime -s` indique que le système a démarré le `2024-10-09 13:54:54`
- `cat /proc/stat | grep processes` permet de connaître le nombre approximatif de processus créés depuis ce démarrage, sur le moment **815** processus. 

2.2/
Pour afficher la liste des processus incluant le PPID (Parent PID), j’ai utilisé `ps -e -o user,pid,ppid,%cpu,%mem,stat,start,time,command `

Pour afficher la liste ordonnée de tous les processus ancêtres de la commande ps en cours d’exécutionn on peut utiliser la comande : `ps -o pid,ppid,command`
```
PID    PPID COMMAND
    773     767 -bash
    817     773 ps -o pid,ppid,command
```

2.3/
Après avoir installé le paquet psmisc avec `apt install psmisc` pour utiliser la commande pstree -ps <PID> pour afficher l’arborescence des processus ancêtres d’un processus spécifique. Ici avec `pstree -ps 773` montre que le processus est descendant de systemd. 
```systemd(1)───sshd(718)───sshd(767)───bash(773)───pstree(874)```

2.4/
En utilisant `top`et en appuyant sur la touche `M`, j’ai trié les processus par utilisation décroissante de la mémoire.
```
 PID UTIL.     PR  NI    VIRT    RES    SHR S  %CPU  %MEM    TEMPS+ COM.
      1 root      20   0  167812  12268   9200 S   0,0   0,1   0:01.26 systemd
    767 root      20   0   18000  11348   9452 S   0,0   0,1   0:01.14 sshd
    231 root      20   0   32968  10816   7660 S   0,0   0,1   0:00.48 systemd-journal
    492 root      20   0   18952  10612   8880 S   0,0   0,1   0:00.05 systemd
    718 root      20   0   15428   9304   7980 S   0,0   0,1   0:00.02 sshd
    443 root      20   0   25412   7932   6904 S   0,0   0,1   0:00.22 systemd-logind
    405 systemd+  20   0   90080   6564   5684 S   0,0   0,1   0:00.37 systemd-timesyn
    259 root      20   0   26320   6008   4636 S   0,0   0,1   0:00.14 systemd-udevd
    465 root      20   0   16532   5796   4932 S   0,0   0,0   0:00.10 wpa_supplicant
    889 root      20   0   11708   5152   3228 R   0,0   0,0   0:00.03 top                                                  441 message+  20   0    9244   4892   4312 S   0,0   0,0   0:00.35 dbus-daemon
    773 root      20   0    7996   4720   3404 S   0,0   0,0   0:00.11 bash
    743 root      20   0    8000   4668   3348 S   0,0   0,0   0:00.01 bash
    735 root      20   0    6120   3852   3328 S   0,0   0,0   0:00.04 login
    417 root      20   0    5868   3636   2796 S   0,0   0,0   0:00.00 dhclient
    493 root      20   0  168700   3072      0 S   0,0   0,0   0:00.00 (sd-pam)
    437 root      20   0    6612   2716   2460 S   0,0   0,0   0:00.03 cron
      2 root      20   0       0      0      0 S   0,0   0,0   0:00.02 kthreadd
      3 root       0 -20       0      0      0 I   0,0   0,0   0:00.00 rcu_gp
      4 root       0 -20       0      0      0 I   0,0   0,0   0:00.00 rcu_par_gp 
```
Le processus `systemd` était celui consommant le plus de mémoire. Il correspond au premier processus lancé au démarrage de la machine, servant de processus initial pour le système Linux. Il gère les services et le démarrage des autres processus.  

Les commande interatif sont `z` pour Passer l’affichage en couleur, `x` pour Mettre en avant la colonne de tri, `M` pour trier par utilisation de la mémoire, `P` pour trier par utilisation du CPU, `N` pour trier par le numéro de PID.

J’ai également installé htop, une alternative plus conviviale à top avec la commande `apt install htop`, offrant une interface colorée et des options de tri et de filtrage améliorées. Cependant son inconvénient est qu’elle n’est pas toujours installé par défaut sur toutes les distributions Linux, il faut donc l'installer. 

## 3/ Arrêt d’un processus

Pour crée le fichier date.sh et data-toto.sh, avec le terminal on crée d’abord un nouveau dossier ‘scripts’ pour mieux organiser,`mkdir -p ~/scripts` , `cd ~/scripts`, `nano date.sh` et on entre le code.

Il faut avoir les permission pour exécuter, donc on donne la permission au propriétaire avec les commande suivante : `chmod 700 ./date.sh` et `chmod 700 ./date-toto.sh`, afin de vérifier le changement `ls -l ./date.sh`. On exécute les fichier avec `./date.sh`, on le met en arrière plan avec CTRL+Z, on refait pareil avec `date-toto.sh`

La commande `jobs` affiche la liste des tâches en arrière-plan avec leurs numéros. 
```
[1]-  Stoppé                 ./date.sh
[2]+  Stoppé                 ./date-toto.sh
```
La commande `fg` permet de ramener en avant-plan la dernière tâche en arrière-plan, et ensuite, un appui sur `CTRL+C`permet de l'arrêter. La commande `ps` affiche les processus en cours d'exécution et fournit leur PID, ce qui permet de les terminer avec la commande `kill <PID>`. Cependant, comme je n'arrivais pas à arrêter le processus avec `kill 2199`, j'ai utilisé `kill -9 2199` pour forcer sa fermeture.
```
[1]-  Processus arrêté      ./date.sh
[2]+  Stoppé                 ./date-toto.sh
```

Le script date.sh affiche chaque seconde le mot "date" suivi de l'heure actuelle au format HH:MM, sans retour à la ligne après "date". Le script date-toto.sh affiche, quant à lui, chaque seconde le mot "toto" suivi de l'heure actuelle, ajustée pour être 5 heures en arrière, au format HH:MM, sans retour à la ligne après "toto".

## 4/ Les tubes
- `cat` sert à afficher le contenu des fichiers alors que tee sert à afficher la sortie d'une commande et à la sauvegarder dans un fichier en même temps. 
- `ls | cat` : Affiche le résultat de la commande ls en utilisant cat 
- `ls -l | cat > liste` : Le fichier liste contiendra le détail des fichiers et dossiers 
- `ls -l | tee liste` : La liste détaillée des fichiers est affichée dans le terminal et enregistrée dans le fichier liste 
- `ls -l | tee liste | wc -l` : Affiche le nombre de lignes de la sortie de ls -l 

## 5/ Journal système rsyslog
Le service rsyslog n’étant pas initialement installé, je l’ai installé avec `apt install rsyslog`, puis démarré et vérifié son statut avec systemctl avec `systemctl start rsyslog` et `systemctl status rsyslog`. Actuellement, je suis vers la fin de l’exercice donc le PID du demon est 6664

Les messages des services standards sont généralement enregistrés dans /var/log/messages ou /var/log/syslog. Cependant, sur ma machine, le fichier /var/log/messages est vide, donc les logs sont stockés dans /var/log/syslog 
#### Fonctionnement de cron et tail :
- cron : Démon Unix permettant de planifier l’exécution de tâches répétitives.
- tail -f : Affiche les dernières lignes d’un fichier et suit les nouvelles entrées en temps réel, utile pour surveiller les logs.

La configuration de la rotation des logs se fait via le fichier /etc/logrotate.conf, qui gère la compression, l’archivage et la suppression des fichiers de log après une certaine période.

J’ai utilisé dmesg pour vérifier les informations matérielles détectées par le système, telles que le modèle du processeur et les cartes réseau avec les commande suivantes : 
- `dmesg | grep -i "cpu"`  a révélé un processeur **12th Gen Intel(R) Core(TM) i7-1260P**
- `dmesg | grep -i "network"` a identifié des cartes réseau **Intel(R) PRO/1000 Network Connection** et **Intel(R) PRO/1000 Network Driver**
