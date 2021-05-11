# Description
Vous soupçonnez un de vos employés d'envoyer des photos depuis le réseau interne. Vous avez donc effectué une capture réseau de son poste, à vous de l'analyser.

# Write UP
Lorsque l'on regarde le traffic de la capture Wireshark, on remarque des trames HTTP.

On applique alors un filtre pour ne voir que le traffic HTTP.

On remarque alors qu'une image britney.jpg a été récupéré par le hacker. On récupère via Fichier -> Exporter Objets --> HTTP --> enregister (en séléctionnant l'image britney.jpg)

On l'ouvre et le flag est affiché !

# Flag
`MCTF{Br1tn3Y_Sp3aRs_F4n}`