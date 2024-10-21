
# TP 03 : Shell Bash

## Exercice : Paramètres

Ce script `analyse.sh` affiche le nombre de paramètres, le nom du script, le 3ème paramètre, et la liste des paramètres fournis en argument.

### Contenu du script :

```bash
#!/bin/bash

# Afficher le nombre de paramètres
echo "Bonjour, vous avez rentré $# paramètres."

# Afficher le nom du script
echo "Le nom du script est : $0"

# Vérifier s'il y a un troisième paramètre
if [ $# -ge 3 ]; then
    echo "Le 3ème paramètre est : $3"
else
    echo "Il n'y a pas de 3ème paramètre."
fi

# Afficher la liste de tous les paramètres
echo "Voici la liste des paramètres : $@"
```

### Exécution :

```bash
chmod +x analyse.sh
./analyse.sh param1 param2 param3
```

### Résultat attendu :

```
Bonjour, vous avez rentré 3 paramètres.
Le nom du script est : ./analyse.sh
Le 3ème paramètre est : param3
Voici la liste des paramètres : param1 param2 param3
```

---

## Exercice : Vérification du nombre de paramètres

Le script `concat.sh` prend deux mots en paramètre et les concatène.

### Contenu du script :

```bash
#!/bin/bash
# concat.sh
# Vérification du nombre de paramètres

if [ $# -ne 2 ]; then
    echo "Erreur : Vous devez rentrer exactement 2 paramètres."
    exit 1
fi

# Concaténation des deux paramètres
concat="$1$2"

# Affichage du résultat
echo "Résultat de la concaténation : $concat"
```

### Exécution :

```bash
chmod +x concat.sh
./concat.sh mot1 mot2

```

### Résultat attendu :

```
Résultat de la concaténation : 
root@debian:~# ./concat.sh mot1 mot2
Résultat de la concaténation : mot1mot2

Résultat si il y q d'erreur
root@debian:~# ./concat.sh mot1 mot2 mot3
Erreur : Vous devez rentrer exactement 2 paramètres.


```

---

## Exercice : Argument Type et Droits

Le script `test-fichier.sh` permet de vérifier le type d'un fichier et ses droits d'accès.

### Contenu du script :

```bash
#!/bin/bash
# test-fichier.sh
# Vérification si le fichier existe et affichage de ses informations

if [ $# -ne 1 ]; then
    echo "Erreur : Vous devez passer exactement un fichier en paramètre."
    exit 1
fi

file=$1

# Vérifier si le fichier existe
if [ ! -e "$file" ]; then
    echo "Le fichier "$file" n'existe pas."
    exit 1
fi

# Vérification du type de fichier
if [ -d "$file" ]; then
    echo "Le fichier "$file" est un répertoire."
elif [ -f "$file" ]; then
    echo "Le fichier "$file" est un fichier ordinaire."
else
    echo "Le fichier "$file" est d'un autre type (lien, périphérique, etc.)."
fi

# Vérification des permissions
permissions=""
if [ -r "$file" ]; then
    permissions="lecture"
fi
if [ -w "$file" ]; then
    permissions="$permissions écriture"
fi
if [ -x "$file" ]; then
    permissions="$permissions exécution"
fi

# Affichage des permissions si elles existent
if [ -z "$permissions" ]; then
    echo "Le fichier "$file" n'est pas accessible par l'utilisateur."
else
    echo "Le fichier "$file" est accessible en $permissions."
fi
```

### Exécution :

```bash
chmod +x test-fichier.sh
./test-fichier.sh /etc/hosts
```

### Résultat attendu :

```
root@debian:~# ./test-fichier.sh /c/hosts
Le fichier "/c/hosts" n'existe pas.


root@debian:~# ./test-fichier.sh /etc
Le fichier "/etc" est un répertoire.
Le fichier "/etc" est accessible en lecture écriture exécution.



root@debian:~# ./test-fichier.sh /etc/hosts
Le fichier "/etc/hosts" est un fichier ordinaire.
Le fichier "/etc/hosts" est accessible en lecture écriture.

```

---

## Exercice : Afficher le contenu d’un répertoire

Le script `listedir.sh` permet d'afficher le contenu d'un répertoire en séparant les fichiers et les répertoires.

### Contenu du script :

```bash
#!/bin/bash

# Récupère la liste des fichiers et répertoires
liste_rep=$(ls)
# Récupère le répertoire courant
rep_courant=$(pwd)

# Affiche le répertoire courant
echo "Répertoire Courant: $rep_courant"

# Affiche les répertoires
echo "Répertoires dans: $rep_courant"
for une_entree in $liste_rep; do
    if [ -d "$une_entree" ]; then
        echo "$une_entree"
    fi
done

# Affiche les fichiers
echo "Fichiers dans: $rep_courant"
for une_entree in $liste_rep; do
    if [ -f "$une_entree" ]; then
        echo "$une_entree"
    fi
done
```

### Exécution :

```bash
chmod +x listedir.sh
./listedir.sh
```

### Résultat attendu :

```
root@debian:~# ./listedir.sh
Répertoire Courant: /root
Répertoires dans: /root
Fichiers dans: /root
cancel
concat.sh
date.sh
date-toto.sh
listedir.sh
test-fichier.sh

```

---

## Exercice : Lister les utilisateurs

Le script `listuser.sh` permet d'afficher la liste des utilisateurs définis dans `/etc/passwd` ayant un UID supérieur à 100.

### Contenu du script :

```bash
#!/bin/bash

# If a specific user is passed as an argument, check only that user
if [ "$1" ]; then
    if id -u "$1" >/dev/null 2>&1 && [ "$(id -u "$1")" -gt 100 ]; then
        echo "$1 exists and has UID greater than 100."
    else
        echo "$1 does not exist or has UID less than or equal to 100."
    fi
else
    # Otherwise, list all users with a UID > 100
    echo "List of users with UID greater than 100:"
    awk -F: '$3 > 100 { print $1 }' /etc/passwd
fi
```

### Exécution :

```bash
chmod +x listuser.sh
./listuser.sh
```

### Résultat attendu :

```
root@debian:~# ./listuser.sh toto
toto does not exist or has UID less than or equal to 100.
root@debian:~# ./listuser.sh Jean
Jean exists and has UID greater than 100.

...
```

---

## Exercice : Création utilisateur

Le script `creat_user.sh` permet de créer un compte utilisateur après avoir vérifié que l'utilisateur n'existe pas déjà.
-	login
-	Nom
-	Prenom
-	UID
-	GID
-	Commentaires

### Contenu du script :

```bash
#!/bin/bash

# Global variables
ROOT_UID=0
SUCCESS=0
E_USEREXISTS=70
E_NOTROOT=1

# Check if the script is run as root
if [ "$UID" -ne "$ROOT_UID" ]; then
  echo "You must be root to run this script."
  exit $E_NOTROOT
fi

# Function to check if the user already exists
user_exists() {
  grep -q "^$1:" /etc/passwd
  return $?
}

# Prompt for user details using `read`
read -p "Enter the username (login): " username
read -p "Enter the first name: " firstname
read -p "Enter the last name: " lastname
read -p "Enter the UID: " uid
read -p "Enter the GID: " gid
read -p "Enter comments (description): " comments

# Check if the user already exists before proceeding
user_exists "$username"
if [ $? -eq $SUCCESS ]; then
  echo "User $username already exists. Please choose another username."
  exit $E_USEREXISTS
fi

# Check if a home directory with the same name already exists
if [ -d "/home/$username" ]; then
  echo "A home directory for $username already exists. Please choose another username or remove the directory."
  exit 1
fi

# Prompt for the password after other questions are answered
read -s -p "Enter the password for the user: " pass
echo

# Create the user with the provided information
useradd -m -d /home/"$username" -s /bin/bash -u "$uid" -g "$gid" -c "$comments" "$username"

# Set the password for the user
echo "$username:$pass" | chpasswd

# Check if the user creation was successful
if [ $? -eq $SUCCESS ]; then
  echo "The account for $username has been successfully created."
  echo "User Details:"
  echo "Username: $username"
  echo "Full Name: $firstname $lastname"
  echo "UID: $uid"
  echo "GID: $gid"
  echo "Comments: $comments"
else
  echo "An error occurred while creating the account."
  exit 1
fi

exit 0
```

### Exécution :

```bash
chmod +x creat_user.sh
./creat_user.sh
```

### Résultat attendu :

```
The account for Mikhael has been successfully created.
User Details:
Username: Mikhael
Full Name: Fady Mikhael
UID: 120
GID: 100
Comments: Hello World!
```
