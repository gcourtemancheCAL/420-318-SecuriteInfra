# Contrôle des permissions
## UGO
**U**ser : Les permissions dont dispose l'utilisateur propriétaire. 
**G**roup : Les permissions dont disposent les utilisateurs qui ne sont pas propriétaire, mais qui font partie du groupe propriétaire.
**O**thers : Les permissions pour tous les autres utilisateurs.

### Précisions
- Bien que l'utilisateur *root* dispose de tous les accès sur tous les fichiers et dossiers, certaines applications vont appliquer certaines restrictions selon les permissions identifiées.
- Les groupes auxquels un utilisateur appartient sont chargés à l'initialisation du processus. Il est possible que certaines opérations requièrent une reconnexion avant de prendre effet.

## Les permissions
L'inode d'un fichier contient l'attribut `st_mode` ou `stx_mode`. Cet attribut est un entier de 16 bits représentant le type de fichier ainsi que les permissions pour les différentes entités. La documentation POSIX décrit ces bits ainsi : 

> POSIX refers to the _stat.st_mode_ bits corresponding to the mask **S_IFMT** (see below) as the _file type_, the 12 bits corresponding to the mask **07777** as the _file mode bits_ and the least significant 9 bits (**0777**) as the _file permission bits_. 
> 
> The following mask values are defined for the file type:

| Symbole      | Masque  | Desc                                 |
| ------------ | ------- | ------------------------------------ |
| **S_IFMT**   | 0170000 | bit mask for the file type bit field |
| **S_IFSOCK** | 0140000 | socket                               |
| **S_IFLNK**  | 0120000 | symbolic link                        |
| **S_IFREG**  | 0100000 | regular file                         |
| **S_IFBLK**  | 0060000 | block device                         |
| **S_IFDIR**  | 0040000 | directory                            |
| **S_IFCHR**  | 0020000 | character device                     |
| **S_IFIFO**  | 0010000 | FIFO                                 |
**Notez que les masques/valeurs sont écrites en octal.**

Plus précisément : 
```
TYPE MMM PPP PPP PPP
0000 000 000 000 000  st_mode (16 bits)
     UGS UUU GGG OOO
         RWX RWX RWX
``` 

Si un bit de permission est à 1, cela indique que l'entité en question dispose de la permission en question

Les permissions sont typiquement écrites en octal : 
`755` -> `RWXR-X-RX` : **U** dispose de toutes les permissions, **G** et **O** uniquement de lecture et d'exécution.

Les 3 bits `M` sont des bits spéciaux : *setuid bit*, *setgid bit*, et *sticky bit*.
### Impact des permissions
##### Fichiers
*Read* : Indique si un utilisateur peut lire le fichier.
*Write* : Indique si un utilisateur peut écrire (modifier) dans le fichier.
*Execute* : Indique si un utilisateur pour exécuter le fichier directement comme un programme ou un script.

*setuid*/*setgid* : Sur un fichier exécutable, fait en sorte que le programme soit exécuté avec le **uid**/**gid** propriétaire du fichier respectivement. Cela permet une élévation de privilège limité à certains contexte. Ces bits n'ont aucun effet sur les scripts. 

Ces bits sont peu utilisés de nos jours.

Pour ajouter le bit **setuid** : `chmod u+s <fichier>`
Pour ajouter le bit **setgid** : `chmod u+g <fichier>`
##### Dossiers
*Read* : Permet de lire le dossier. Essentiellement, permet à ls de fonctionner sur un dossier. Retirer l'accès en lecture à un dossier n'est pas suffisant pour empêcher l'accès au dossier ou aux fichiers auxquels il contient.
*Write* : Permet de créer des fichier au sein du dossier, de les déplacer ou de les renommer. N'empêche la modification du fichier en tant que tel.
*Execute* : Permet d'accéder au dossier en tant que tel. On peut donc s'y déplacer avec `cd` et ouvrir les fichiers que le dossier contient. Permet aussi de voir les informations supplémentaires sur les fichiers contenus par le dossier. Ses interactions avec les autres permissions peuvent être non intuitives.  

*sticky bit* : Restreint la suppression des fichiers à leur propriétaire. Ce bit permet de créer un dossier accessible à tous pour y créer des fichiers mais d'empêcher n'importe qui de supprimer ce qui y est présent.

Pour ajouter le *sticky bit* : `chmod +t <dossier>`

## ACL
Permet une gestion des permissions plus avancé.

Pour installer : `apt install acl`

`getfacl fichier/dossier` : Liste l'ensemble des permissions sur un dossier.

![[img/facl1.png]]
Le dossier ci-dessus appartient a root:root et a l'équivalent des permissions 755. Cependant, une exemption existe pour l'utilisateur test. Cet utilisateur à tous les droits.
![[img/facl2.png]]
On voit ici le même fichier, mais cette fois ci on a retiré toutes les permissions à test109. 

Le tableau ci-dessous illustre les permissions effectives :

| Qui         | Permission |
| ----------- | ---------- |
| root:\*     | RWX        |
| \*:root     | RWX        |
| test-ssh:\* | RWX        |
| test109:\*  | ---        |
| \*:\*       | R-X        |
La commande setfacl peut être utilisée pour configurer des permissions. Par exemple, la commande `setfacl -m user:username:rw ./dossier/ou/fichier` ajoute une permissions spéciale pour un utilisateur spécifique. Le même principe fonctionne pour un groupe : `setfacl -m groupe:groupname:rw ./dossier/ou/fichier`

On peut créer des permissions par défaut sur les dossiers. Ces règles seront affectés automatiquement aux fichiers créés au sein de ce dossier. `setfacl -d -m user::rw,group::rw,other:r ./dossier`

## umask
Masque qui affecte les permissions par défaut des fichiers créés. Le *umask* s'applique sur un processus et non un dossier. 

Différents services peuvent souvent se faire assigner un *umask*. Dépendamment du service, cela peut être plus ou moins simple à accomplir. 

On peut vouloir configurer le *umask* pour un utilisateur en particulier. Dans ces situations, on va vouloir modifier le fichier `.profile` de l'utilisateur.

Le `umask` fonctionne comme un masque soustractif. La formule de base est la suivante : 
```
perm = default & ~umask

# Normalement, les permissions par defaut d'un fichier sont de 666. Donc avec un 
# umask de 022 nous aurions le calcul suivant: 
perm = 0666 & (~0022)
perm = 0666 & (0755)
perm = 0644

# En binaire : 
perm = 
 110 110 110 & 
~000 010 010
perm = 
 110 110 110 &  
 111 101 101
perm = 
 111 100 100

# Le résultat serait donc : RW-R--R--
``` 

## sudo
Pour qu'un utilisateur puisse utiliser `sudo`, il doit être membre du groupe `sudo`.

## Stratégies d'allocation des permissions
### Principe du moindre privilège
[Qu'est-ce que le principe du moindre privilège](https://www.cyberark.com/fr/what-is/least-privilege/)

### Principe du moindre privilège
[Qu'est-ce que le RBAC](https://www.entrust.com/fr/resources/learn/what-is-role-based-access-control)

L'utilisation des groupes sur linux permet de définir des privilèges sur la base des rôles de l'utilisateur. Ici, un rôle n'est pas foncièrement le poste d'une personne dans l'entreprise mais bien un représentation des responsabilités et pouvoir qu'un utilisateur dispose.

Une bonne séparation des rôles disponible sur un système vient faciliter l'ajout et la suppression des permissions aux utilisateurs.

Les groupes `sudo` et `systemd-journal` sont des exemples de groupes qui permettent d'octroyer des permissions sur la base des actions que l'on veut qu'un utilisateur puisse entreprendre.

Lors de l'élaboration d'une stratégie d'attribution des permissions, nous allons vouloir nous assurer de restreindre les permissions attribués aux utilisateurs directement et de préconiser l'utilisation des groupes. L'utilisation des ACL va nous permettre de créer des ensembles de règles plus flexibles et complexes englobant mieux notre réalité d'affaire.
## Commandes
- *chmod* : Modifie les permissions sur un fichier ou un dossier.
- *chown* : Modifie l'appartenance d'un fichier ou d'un dossier.
- *getfacl* : Affiche les accès sur un fichier
- *setfacl* : Permet de modifier les accès sur un fichier.