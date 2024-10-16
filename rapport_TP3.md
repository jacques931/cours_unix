# Compte rendu de services et processus signaux
Comme, je suis sur Window, j’utilise le terminal de git bash pour executer les scripts quand il n’est pas necessaire de tester avec la machine virtuel pour aller plus vite 

Exercice : paramètres

Code analyse.sh :
```
echo "Bonjour, vous avez rentré $# paramètres"
echo "Le nom du script est $(basename $0)"
echo "Le 3ème paramètre est $3"
echo "Voici la liste des paramètres : $@"
```

```
$ ./analyse.sh param1 param2 param3 param4
Bonjour, vous avez rentré 4 paramètres
Le nom du script est analyse.sh
Le 3ème paramètre est param3
Voici la liste des paramètres : param1 param2 param3 param4
```
Exercice : vérification du nombre de param`etres
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

```
$ ./concat.sh param1 param2
Resultat : param1param2
$ ./concat.sh param1
Vous devez avoir que 2 paramètres pas plus pas moins

```

Exercice : argument type et droits
Code test_fichier,sh :
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
