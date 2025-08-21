# Attaques #

## Ingénierie sociale ##
L’**ingénierie sociale** est une famille d’attaques qui vise à **manipuler les personnes** plutôt que de s'attaquer au système informatique directement.  

Elle repose sur la tromperie (ex. se faire passer pour une personne de confiance, créer un sentiment d’urgence ou de peur) afin d’amener une victime à **révéler des informations sensibles**, **donner accès à un système** ou **poser une action risquée**.

C'est souvent la voie la plus simple.
## Logiciels malveillants ##

### Classification par mode de propagation ###
#### Virus ####
Un virus est un type de logiciel malveillant qui va infecter des fichiers sur le système afin d'assurer sa propagation. 

Bien qu'autonome une fois activée, un virus nécessite tout de même qu'un utilisateur exécute un fichier infecté pour s'exécuter.
#### Vers / Worm ####
Un vers est un type de logiciel malveillant qui se propage de façon autonome à travers le réseau. Un vers ne va pas nécessiter d'interaction de la part de l'utilisateur.
#### Cheval de Troie / Trojan ####
Logiciel malveillant qui se présente comme un logiciel utile afin de tromper un utilisateur à l'exécuter.

Exemple: 
- SUPER_RARE_OF_LEAK.mp4.exe
- Tribes_CompleteRIP.exe
### Classification par comportement ###
Ce qui suit est une liste partielle et non exhaustive. 
#### Logiciel espion / Spyware
Un **logiciel espion** est un programme malveillant qui vise à **surveiller l’utilisateur** et de **collecter ses informations personnelles** (ex. mots de passe, habitudes de navigation, données sensibles).
#### Rançongiciel / Ransomware
Un **rançongiciel** est un logiciel malveillant qui **bloque l’accès aux fichiers ou au système** en les chiffrant et qui exige le paiement d’une **rançon** (souvent en cryptomonnaie) pour les débloquer.
#### Outil d'accès à distance / RAT
Un **outil d'accès à distance** est un type de logiciel malveillant qui donne à un attaquant un **contrôle à distance complet** sur l’ordinateur infecté, comme s’il était assis devant.

Il existe des versions entièrement légitime de ces logiciels (par exemple TeamViewer et AnyDesk). Les cybercriminels peuvent essayer d'utiliser l'ingénierie sociale afin de convaincre des utilisateurs d'installer ces logiciels et de leur fournir accès à leur système.
#### Botting
Logiciel malveillant qui permet de contrôler un système à distance afin d'effectuer des tâches à l'insu de l'utilisateur. Souvent joints à un botnet - soit un réseau de bots. Les botnets sont souvent utilisés lors d'attaques par dénis de service.
#### Mineur clandestin / Cryptojacking
Logiciel malveillant qui utilise les ressources d'un système afin de miner des cryptomonnaies à l'insu de l'utilisateur.
## Man in the middle ##
Type d'attaque durant laquelle un tiers parti se place **entre deux parties qui communiquent** afin d'**écouter ou modifier** les informations échangées, sans que les victimes ne s’en rendent compte.

![mitm](img/mitm.webp)
## Attaque par déni de service ##
Une attaque par déni de service (**DoS**) est une attaque visant à rendre un service inaccessible. Lorsqu'une grande quantité de système est utilisée pour effectuer l'attaque (e.g botnet), on va parler de "**déni de service distribué**" ou **DDoS**.

