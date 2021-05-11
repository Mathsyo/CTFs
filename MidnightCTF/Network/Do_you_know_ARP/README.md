# Description
Cette capture réseau est vide, à moins que ... ?

# Write UP
`tshark -r capture.pcapng -T fields -e arp.dst.hw_mac | sed ‘s/.*://’ | xxd -r -p` ce oneliner donne le flag (il permet de recuperer les mac address, puis de prendre les 2 derniers bytes et enfin de les convertir)

# Flag
`MCTF{M@c_P0w3r}`