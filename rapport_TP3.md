# Compte rendu commande shell bash
Comme, je suis sur Window, j’utilise le terminal de git bash pour executer les scripts quand il n’est pas necessaire de tester avec la machine virtuel pour aller plus vite 

### Exercice : paramètres

Code analyse.sh :
```
echo "Bonjour, vous avez rentré $# paramètres"
echo "Le nom du script est $(basename $0)"
echo "Le 3ème paramètre est $3"
echo "Voici la liste des paramètres : $@"
```
Résultat :
```
$ ./analyse.sh param1 param2 param3 param4
Bonjour, vous avez rentré 4 paramètres
Le nom du script est analyse.sh
Le 3ème paramètre est param3
Voici la liste des paramètres : param1 param2 param3 param4
```

### Exercice : vérification du nombre de param`etres
Code concat.sh : 
```
nb=2
if [ $# -eq $nb ];then
    concat=$1$2
    echo "Resultat : $concat"
else
    echo "Vous devez avoir que 2 paramètres pas plus pas moins"
fi

```
Résultat
```
$ ./concat.sh param1 param2
Resultat : param1param2
$ ./concat.sh param1
Vous devez avoir que 2 paramètres pas plus pas moins

```

### Exercice : argument type et droits
Code test_fichier.sh :
```
if [ -z $1 ] || [ ! -e $1 ]; then
    echo "Veuillez fournir un fichier valide en paramètre."
    exit 1
fi

if [ -d $1 ]; then
    echo "Le fichier $1 est un répertoire"
else 
    if [ -f $1 ] && [ -s $1 ]; then
    echo "Le fichier $1 est un fichier ordinaire qui n’est pas vide"
    else
        echo "Le fichier $1 n'est pas un répertoire ou un fichier ordinaire vide"
    fi
fi

# Permission
perm=""
if [ -r "$1" ]; then
    perm+="lecture "
fi

if [ -w "$1" ]; then
    perm+="écriture "
fi

if [ -x "$1" ]; then
    perm+="exécution"
fi

if [ -z "$perm" ]; then
    echo $1 "n'est pas accessible par $(whoami)."
else
    echo $1 "est accessible par $(whoami) en $perm"
fi

```

### Exercice : Afficher le contenu d’un répertoire
Code listdir.sh :
```
# Vérifie que le nombre de paramètres est exactement égal à 1
if [ $# -ne 1 ]; then
    echo "Il ne faut q'un seul parametre"
    exit 1
fi

if [ -d "$1" ]; then
    # Affichage des fichiers
    echo "####### Fichiers dans $1"
    ls -p "$1" | grep -v /

    # Affichage des répertoires
    echo "####### Répertoires dans $1"
    ls -p "$1" | grep /
else
    echo "Le paramètre n'est pas un répertoire"
fi
```
### Exercice : Lister les utilisateurs

Le problème avec la commande for user in $(cat /etc/passwd); do echo $user; done est que cela parcourt chaque ligne du fichier /etc/passwd en la scindant par des espaces, ce qui peut causer des problèmes pour les lignes contenant des espaces dans leurs champs, et donc ne pas traiter correctement chaque ligne comme une unité.

Voici mon fichier /etc/passwd : 
```
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
_apt:x:42:65534::/nonexistent:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-network:x:998:998:systemd Network Management:/:/usr/sbin/nologin
systemd-timesync:x:997:997:systemd Time Synchronization:/:/usr/sbin/nologin
messagebus:x:100:107::/nonexistent:/usr/sbin/nologin
avahi-autoipd:x:101:109:Avahi autoip daemon,,,:/var/lib/avahi-autoipd:/usr/sbin/nologin
sshd:x:102:65534::/run/sshd:/usr/sbin/nologin
```

Code listuser.sh :
```
#Commande avec cut
for user in $(cut -d':' -f 1 /etc/passwd); do
    uid=$(grep "^$user:" /etc/passwd | cut -d':' -f 3)
    if [ "$uid" -gt 100 ]; then
        echo $user
    fi
done

#Commande avec awk
awk -F: '$3 > 100 {print $1}' /etc/passwd
```

Resultat : 
```
nobody
systemd-network
systemd-timesync
avahi-autoipd
sshd
```

### Exercice : Mon utilisateur existe t’il
Code existuser.sh:
```
# Vérification du nombre d'arguments
if [ $# -ne 1 ]; then
    echo "Vous devez avoir 1 paramètre : login ou uid"
    exit 1
fi

result=""

# Vérification par login (si $1 est un nom d'utilisateur)
if [ -n "$1" ]; then
    result=$(awk -F: -v login="$1" '$1 == login {print $3}' /etc/passwd)
fi

# Si aucun résultat trouvé par login, vérifier par UID
if [ -z "$result" ]; then
    result=$(awk -F: -v uid="$1" '$3 == uid {print $3}' /etc/passwd)
fi

# Si un résultat est trouvé, l'afficher et retourner succès
if [ -n "$result" ]; then
    echo "L'utilisateur a un UID de $result"
    exit 0
else
    # Si aucun résultat trouvé, retourner un code d'erreur
    exit 1
fi
```

### Exercice : Creation utilisateur
Code useradd.sh:
```
# Fonction pour vérifier si le script est exécuté par root
if [ "$USER" != "root" ]; then
  echo "Ce script doit être exécuté en tant que root."
  exit 1
fi

# Importe le fichier de verification
existuser_script="./existuser.sh"

# Demander les informations nécessaires pour l'utilisateur
read -p "Entrez le login de l'utilisateur : " login
read -p "Entrez le nom de l'utilisateur : " nom
read -p "Entrez le prénom de l'utilisateur : " prenom
read -p "Entrez l'UID souhaité (laisser vide pour un UID par défaut) : " uid
read -p "Entrez le GID souhaité (laisser vide pour un GID par défaut) : " gid
read -p "Entrez les commentaires (description de l'utilisateur) : " comments

# Vérifier si l'utilisateur existe déjà
./existuser.sh "$login"
if [ $? -eq 0 ]; then
  echo "Un utilisateur avec ce login existe déjà."
  exit 1
fi

# Vérifier si un utilisateur avec cet UID existe déjà
if [ -n "$uid" ]; then
  ./existuser.sh "$uid"
  if [ $? -eq 0 ]; then
    echo "Un utilisateur avec cet UID existe déjà."
    exit 1
  fi
fi

# Vérifier si le répertoire /home/login existe déjà
if [ -d "/home/$login" ]; then
  echo "Le répertoire /home/$login existe déjà."
  exit 1
fi

# Construction des options pour useradd
options="-m -d /home/$login -c \"$comments\""

# Ajouter les options pour UID et GID si spécifiées
if [ ! -z "$uid" ]; then
  options="$options -u $uid"
fi

if [ ! -z "$gid" ]; then
  options="$options -g $gid"
fi

# Créer l'utilisateur
useradd $options "$login"

# Vérifier si la création a réussi
if [ $? -eq 0 ]; then
  echo "L'utilisateur $login a été créé avec succès."
else
  echo "Erreur lors de la création de l'utilisateur $login."
  exit 1
fi
```
