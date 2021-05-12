# Description
La NASA vient de recevoir les premières images de son nouveau rover marsien Perseverance
Depuis quelques heures, le Command Control aurait des problèmes pour envoyer les instructions au rover... Selon eux, le vent martien brouillerait les communications avec le rover.
En tant que fin connaisseur, vous sentez que quelque chose cloche, mais quoi...
Votre but est de trouver ce détail et de prévenir la NASA au plus vite.
Bonne chance citoyenne, citoyen !

# Write UP

## 1
![https://alexandre-didier.me/content/images/2021/04/code-gif.png](https://alexandre-didier.me/content/images/2021/04/code-gif.png)

"Strong archive" ? Ok, on passe donc un coup de `binwalk` sur notre gif.

![https://alexandre-didier.me/content/images/2021/04/binwalk-2.png](https://alexandre-didier.me/content/images/2021/04/binwalk-2.png)

Il y a 1 fichier caché qui contient plein de choses que l'on va simplement extraire avec `binwalk -e -c Perseverance.gif`:

![https://alexandre-didier.me/content/images/2021/04/extract-binwalk.png](https://alexandre-didier.me/content/images/2021/04/extract-binwalk.png)

Les fichiers texte contiennent un message avec du morse disant de bien regarder l'atterrissage...
L'archive .rar, elle, pourra être ouverte avec le mot de passe qui était affiché dans le gif : `My_StR0n9_4rCh1v3`

Elle contient une vidéo .mp4 du déploiement du parachute de 
Perseverance. Celle-ci fait 2 minutes 22 secondes. Étrange... En faisant
 une petite recherche, on peut retrouver l'originale [ici](https://www.youtube.com/watch?v=BsXFIbe-5y4) qui ne fait que 21 secondes et n'as pas de son...

Binwalk ne donne rien... Examinons alors les données EXIF avec

```
exiftool
```

:

![https://alexandre-didier.me/content/images/2021/04/exif-result.png](https://alexandre-didier.me/content/images/2021/04/exif-result.png)

Beaucoup d'info avec `exiftool`, mais le plus intéressant est sur la capture ci-dessus.
Le fichier a été modifié avec Adobe Premiere Pro et on voit qu'un 
fichier test.wav a été ajouté. Le son n'étant pas d'origine, on va 
devoir l'extraire et l'analyser.

[Ici j'ai passé longtemps à me battre avec Audacity, c'était un enfer...]

Donc en jouant un peu avec Audacity et le spectre du son on peut appercevoir des characters ?

![https://alexandre-didier.me/content/images/2021/04/audacity--1.png](https://alexandre-didier.me/content/images/2021/04/audacity--1.png)

Affinons...

![https://alexandre-didier.me/content/images/2021/04/audacity--2.png](https://alexandre-didier.me/content/images/2021/04/audacity--2.png)

Encore ?

![https://alexandre-didier.me/content/images/2021/04/audacity-3.png](https://alexandre-didier.me/content/images/2021/04/audacity-3.png)

Ok... `ONXH{Aitdixpvcygg}` ?
Ça a la forme d'un flag, mais avec un chiffrement par substitution ou par décalage ?

On peut penser que `ONXH` serai `MCTF` donc avec un rapide calcule ce n'est pas un Caesar...

Un vigenère ? Si oui, avec quelle clé ?

Le site dcode peut-il nous aider ?

![https://alexandre-didier.me/content/images/2021/04/dcode-1.png](https://alexandre-didier.me/content/images/2021/04/dcode-1.png)

On indique ce que l'on est sûr d'avoir...

![https://alexandre-didier.me/content/images/2021/04/dcode-2.png](https://alexandre-didier.me/content/images/2021/04/dcode-2.png)

Wait... La clé de chiffrement ne serait que

```
CLE
```

???

![https://alexandre-didier.me/content/images/2021/04/dcode.png](https://alexandre-didier.me/content/images/2021/04/dcode.png)

## 2
1 – Un gif du rover Perseverance contenant une archive ZIP.
2 – Dans l’archive, on trouve : • Un fichier txt • Une archive protéger par un mdp
3 – Sur le GIF, récupérer le mdp de l'archive "Archive.rar"
4 – Dans l’archive, on trouve la vidéo qu’il faut ouvrir avec Audacity pour trouver le flag cacher dans le spectrogramme.

Ouvrir le fichier au format mp4 dans Audacity

Puis, on sélectionne l'option "Spectrogramme" dans le menu déroulant de notre piste audio.


On modifie la vitesse de lecture de notre piste audio au maximum.

On trouve un flag obfusquer en Vigenère avec l'alphabet ABCDEFGHIJKLMNOPQRSTUVWXYZ et la clé CLE

On le déchiffre via dcode

Voilà, vous venez de trouver le flag !

# Flag
`MCTF{Perseverance}`
