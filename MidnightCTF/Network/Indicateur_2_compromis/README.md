# Description
Vous avez récupéré un PC particulièrement vérolé... Le groupe TA505 est particulièrement actif en ce moment, vous pouvez vous rapprocher des autorités compétentes pour en savoir plus

# Write UP
Il se passe beaucoup de choses dans le pcap fourni. La très grande 
majorité des paquets semble être du trafic TLS, avec quelques échanges 
HTTP en clair et des paquets DNS, DHCP et DNS. Rien de très surprenant 
ici, nous avons affaire à une personne qui navigue sur internet.

![https://blog.h25.io/images/2021-04-midnightflag/compromis1.png](https://blog.h25.io/images/2021-04-midnightflag/compromis1.png)

Cependant, on remarque aussi certains paquets UDP contenant des données qui semblent intéressantes. En écrivant `udp` dans le filtre Wireshark en haut de la fenêtre, les 206 paquets UDP de la capture se révèlent à nous :

![https://blog.h25.io/images/2021-04-midnightflag/compromis2.png](https://blog.h25.io/images/2021-04-midnightflag/compromis2.png)

L’un des premiers paquets contient le texte `TUNURntuZXZlcl9nb25uYV9naXZlX3lvdV91cA==` qui semble être du base64. En le décodant, on obtient le texte `MCTF{never_gonna_give_you_up`.
 C’est évidemment un faux flag, mais la présence de plusieurs autres 
paquets de ce type laissent à penser que le flag correct se trouverait 
parmi eux.

On peut extraire le contenu des paquets UDP très simplement, en 
utilisant l’outil Tshark qui est l’équivalent en ligne de commandes de 
Wireshark.

![https://blog.h25.io/images/2021-04-midnightflag/compromis3.png](https://blog.h25.io/images/2021-04-midnightflag/compromis3.png)

Sur chaque ligne apparaît un flag potentiel, on peut faire un petit script en Python ou en Bash pour les décoder une à une :

![https://blog.h25.io/images/2021-04-midnightflag/compromis4.png](https://blog.h25.io/images/2021-04-midnightflag/compromis4.png)

![https://blog.h25.io/images/2021-04-midnightflag/compromis5.png](https://blog.h25.io/images/2021-04-midnightflag/compromis5.png)

Sur l’une des lignes, on repère le flag `MCTF{IOC_R_fun}`
 qui semble être le moins fake de tous, et qui est effectivement le bon.
 C’est une technique assez maudite de créer des challenges qui ont des 
faux flags, surtout en aussi grand nombre et quasiment indiscernables du
 vrai, mais chaque créateur de CTF est libre de choisir son design et ce
 n’était ici pas trop gênant. On conclura donc ce writeup sur les sages 
paroles des amis Unactive et Mahal

# Flag
MCTF{IOC_R_fun}