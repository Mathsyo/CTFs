# Description
La curiosité a toujours été une vertue, certains l'ont, d'autres non...
Vous n'avez rien vu lors de votre inscription ?
Accès au challenge : `https://midnightflag.fr/inscription.html`

# Write UP
Lorsqu'on rentre une commande inconnue, la page répond en affichant cette dite commande
![image](https://user-images.githubusercontent.com/39094311/116255274-60f96700-a772-11eb-9e68-04806883ac3e.png)
Ici, on peut donc tenter une XSS avec une balise image:
![image](https://user-images.githubusercontent.com/39094311/116255304-6787de80-a772-11eb-9f43-864c7498bfe8.png)
Une fois que l'alert a été déclenché, on se voit rediriger vers une page qui nous donne le flag:
![image](https://user-images.githubusercontent.com/39094311/116255318-6b1b6580-a772-11eb-9bbb-a2a42ae5dde7.png)


# Flag
`MCTF{f1rst_w3b_ch4ll3ng3_!!}`
