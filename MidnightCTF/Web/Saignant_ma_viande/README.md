# Description
Une boucherie vend de la viande (qui a l'air très bonne) sur un site internet.
Un hunter anonyme vous a transmis ce message :
Le site est bizarre et les commentaires aussi.... je suis sur qu'il cache une autre facette de vente.
A vous d'investiguer !
Accès au challenge : `https://135.125.232.20:8443/`

# Write UP
En arrivant sur le challenge, on remarque directement que le challenge tourne sous un serveur chiffrant les communications (https).
On observe les commentaires, et en effet, un en particulier est bizarre puisqu'il est en Russe et indique "Merci pour la vente"

Dans le fichier robots.txt, on peut remarquer la ligne:
![image](https://github.com/MidnightFlag/CTF2021/blob/main/Web/Saignant/images/robots.png)

Par ailleurs, sur la page principale du site, tout en bas, on remarque:
![image](https://github.com/MidnightFlag/CTF2021/blob/main/Web/Saignant/images/mainpage.png)

On cherche donc une faille en rapport avec OpenSSL et "2014", et on tombe directement sur la faille du nom de "heartbleed", qui permet de leak de la mémoire d'un serveur.
Pour l'exploitation, j'ai choisi d'utiliser le module metasploit pour exploiter la faille:
![image](https://github.com/MidnightFlag/CTF2021/blob/main/Web/Saignant/images/metasploit1.png)
![image](https://github.com/MidnightFlag/CTF2021/blob/main/Web/Saignant/images/metasploit2.png)

Dans ce leak mémoire, on remarque que la chaîne de caractères "7cf8a002dbc08c3f31aca619141d141d" reviens très régulièrement, on peut donc supposer que c'est le nom du fichier ou du dossier que l'on cherche.
![image](https://github.com/MidnightFlag/CTF2021/blob/main/Web/Saignant/images/secret_folder.png)

Notre intuition était bonne, on tombe bien sur le dossier caché, on essaye donc de cliqué pour commander une arme auprès des tontons flingueurs:
![image](https://github.com/MidnightFlag/CTF2021/blob/main/Web/Saignant/images/secret_folder.png)


# Flag
`MCTF{t0nt0n_fl1ngu3rs_g3t_pwn3d}`