# Description
Vous avez intercepté des échanges sur un réseau legacy Ethernet 10mbps. Pourrez vous récupérer les données encodées ?

# Write UP
Le fichier texte fourni donne une chaine de caractères binaires. Deux indices dans le titre/intitulé : codage et Ethernet 10Mbps. Le codage utilisé sur cette technologie est le codage Manchester. La payload peut être lue à la main mais il existe un tool en ligne utile :

https://www.dcode.fr/manchester-code
![image](https://camo.githubusercontent.com/441c7ffb5ce950b2a7af2fe3724f932277f31fe3f72d14613ba411c672e24e28/68747470733a2f2f692e696d6775722e636f6d2f3546577a486c4f2e706e67)
Cela nous donne une chaine binaire, qu'il suffit de passer en ASCII :
![image](https://camo.githubusercontent.com/1c2d3fe4bd137956b54e280f417a39588e18547d39c8d7927ca40a11da23e293/68747470733a2f2f692e696d6775722e636f6d2f485764795556672e706e67)

# Flag
mctf{g0_m4nCh35t3R}