# Description
Un jeune développeur débutant vient de commencer à coder un site web, il a implémenté un processus d’authentification afin de segmenter les droits utilisateurs.
En tant que débutant, il ne connaît pas trop les vulnérabilités et n'utilise pas forcément les versions à jour.
Accès au challenge : `http://fcuj.ctf.midnightflag.fr/`

# Write UP
Le challenge parle de "faux jeton". On se voit donné de quoi se connecter comme deux utilisateurs différents.
En regardant ses cookies une fois connecté on constate qu'il s'agit de JWT, qu'on peut décoder par exemple sur [https://jwt.io/](https://jwt.io/).
On y remarque que l'algorithme est `RS256` (RSA) et "isAdmin": 0; notre objectif sera de générer un token valide (dont la signature est acceptée par le serveur) avec `"isAdmin": 1`.
En mettant de côté la surréelle attaque de l'algorithme `none`, l'attaque la plus courante sur les JWT est "RSA or HMAC", qui consiste à
changer l'algorithme de son token `RS256` en `HS256` après l'avoir signé avec la clé publique servant initialement à vérifier les signatures.
Ce faisant le serveur, muni d'un token, de la clé publique, et d'un algorithme symétrique, utilisera la clé publique comme clé symétrique pour valider.
L'intérêt est justement que cette clé est néanmoins "publique".
Elle n'est pas pour autant donnée, mais deux JWT suffisent normalement à la récupérer. On peut utiliser un script existant comme [https://github.com/FlorianPicca/JWT-Key-Recovery](https://github.com/FlorianPicca/JWT-Key-Recovery) pour ce faire.
Il ne reste plus qu'à utiliser le résultat (le PEM de clé publique retrouvé) comme clé pour chiffrer son nouveau token. On pensera à grossir la valeur exp,
quand expire le token, pour éviter tout ennui.
On peut générer le token avec par exemple `pyjwt`, après avoir retiré la sécurité visant justement à prévoir cette exploitation :
`jwt.encode({"user": "user1","exp": 1628091330,"isAdmin": 1}, cle_publique, algorithm="HS256").decode()`
Et finalement récupérer la page du flag:
`curl -L [http://fcuj.ctf.midnightflag.fr/admin](http://fcuj.ctf.midnightflag.fr/admin) -X POST -b token=TOKEN`

# Flag
`MCTF{r54W17H7W070K3N}`