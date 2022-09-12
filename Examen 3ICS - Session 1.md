# Examen 3ICS - Session 1

## Exercice 2. Questions de cours détaillées

1. Expliquez la différence entre :
- commande1 ; commande2
- commande1 && commande2
- commande1 || commande2

La première commande execute commmande1 puis commande2 à la suite.

La deuxième commande execute commande1 puis commande2 si commande1 a réussi.

La troisième commande execute commande1 puis commande2 si commande1 a échoué.

2. On définit une valeur de umask à 141 (peu importe si cette valeur est pertinente dans un cas réel). Quels droits posséderont les nouveaux fichiers et dossiers créés ? Justifiez la réponse.

Les nouveaux fichiers auront les droits rw-r--r-- et les nouveaux dossiers auront les droits rwxr-xr-x.

3. Quelle est la différence entre les signaux TERM et KILL ?

Le signal TERM indique à un processus de s'arrêter proprement. Le signal KILL indique à un processus de s'arrêter immédiatement.

## Exercice 3. Redirections et pipelines 

```bash
unzip copies.zip 2> /var/log/syslog | analyse 2> erreur_analyse.txt | correction 2> erreur_correction.txt 1> /notes.csv
```

# Exercice 4. Script Bash

La commande stat -c %a fichier permet de récupérer la valeur numérique des droits sur fichier. Par exemple, si le fichier exemple.txt possède les droits r-x-w----, la commande stat -c %a exemple.txt affiche 520.
On vous demande d’écrire un script Bash appelé afficheDroits qui affiche en français les droits d’un fichier passé en paramètre sur la ligne de commande. Par exemple, avec le fichier exemple.txt précédent, on obtiendrait le résultat suivant :

```bash
$ ./afficheDroits exemple.txt
Droits sur exemple.txt :
Utilisateur : lecture, exécution
Groupe : écriture
Autres :
```

```bash
#!/bin/bash

if [ $# -ne 1 ]; then
    echo "Usage : $0 fichier"
    exit 1
fi

if [ ! -f $1 ]; then
    echo "$1 n'est pas un fichier"
    exit 1
fi

droits=$(stat -c %a $1)
droits_utilisateur=()
droits_groupe=()
droits_autres=()

if [ $((droits / 100)) -ge 4 ]; then
    droits_utilisateur+=("lecture")
fi

if [ $((droits / 100)) -ge 2 ]; then
    droits_utilisateur+=("écriture")
fi

if [ $((droits / 100)) -ge 1 ]; then
    droits_utilisateur+=("exécution")
fi

if [ $((droits / 10 % 10)) -ge 4 ]; then
    droits_groupe+=("lecture")
fi

if [ $((droits / 10 % 10)) -ge 2 ]; then
    droits_groupe+=("écriture")
fi

if [ $((droits / 10 % 10)) -ge 1 ]; then
    droits_groupe+=("exécution")
fi

if [ $((droits % 10)) -ge 4 ]; then
    droits_autres+=("lecture")
fi

if [ $((droits % 10)) -ge 2 ]; then
    droits_autres+=("écriture")
fi

if [ $((droits % 10)) -ge 1 ]; then
    droits_autres+=("exécution")
fi

echo "Droits sur $1 :"
echo "Utilisateur : ${droits_utilisateur[*]}"
echo "Groupe : ${droits_groupe[*]}"
echo "Autres : ${droits_autres[*]}"
```

## Exercice 5. Gestion des disques / Systèmes de fichier 

1. Sous Linux, on peut copier, déplacer ou supprimer des fichiers. Expliquez (précisément) pourquoi le fait de déplacer un fichier sur la même partition peut être beaucoup plus rapide que copier ce fichier puis supprimer l’original (notamment pour les fichiers volumineux) ?

Car le déplacement d'un fichier consiste à changer le lien physique du fichier, alors que la copie d'un fichier consiste à copier le contenu du fichier.

2. Un fichier supprimé est-il supprimé du disque dur ? Existe-t-il des moyens de le retrouver ?

Un fichier supprimé est supprimé du disque dur. Il est possible de le retrouver avec la commande `strings` ou `foremost`.

3. En quoi consiste exactement le formatage d’une partition ? Les données que contenait la partition sont-elles effacées ? Définitivement perdues ?

Le formatage consiste à effacer les données de la table de partitions et à créer un système de fichier dessus.