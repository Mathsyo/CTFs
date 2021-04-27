# Description
Une alarme pilotée par un arduino a été cachée dans les faux plafonds de l'ESNA. Pour partir en pause plus tôt, vous avez pour projet de déclencher l'alarme à distance pour que les élèves soient évacués de la classe. Vous avez réussi à prendre la main sur la machine qui pilote l'arduino, mais il vous manque un code secret pour activer l'alarme.
Sans doute arriverez-vous à le retrouver en analysant le fichier .hex qui a été chargé sur l'arduino en question !

# Write UP

## 1
```
$ avr-objcopy -I ihex challenge.hex -O binary firmware.bin && strings firmware.bin | grep MCTF
```

## 2
Tout le défi ici se trouve dans la recherche de l'outil et des commandes à utiliser pour reverse un fichier ihex arduino. On constate en effet que les outils classiques tels que strings, objdump et tout ne sont pas d'une grande utilité à cause du format de ce fichier.
* Si on n'aime pas télécharger d'outils, on peut se renseigner sur le format de fichier ihex et se rendre compte que les données qui y sont présentes sont simplement encodées en hexa : si on cherche "MCTF" en hexa (4d435446) dans le fichier, on trouve l'endroit où se trouve le flag (grep -i 4d435446 challenge.hex) et on peut le reconstituer à partir de notre connaissance du format de fichier ihex.
* Si on a avr-objdump, un simple avr-objdump -s -j .sec1 -m avr5 challenge.hex fait l'affaire !
*
## 3
```python
#!/usr/bin/python

from pwn import *
from z3 import *
from string import printable

elf = ELF('./KeeCustom.bin')

key = elf.read(0x2020, 0x30)

s = Solver()

def add(a, b):
    s = (a & 0xff) + (b & 0xff)
    return (s << 4) ^ (s >> 4)

def sub(a, b):
    return a ^ b

def multiply(a, b):
    return a+b

def divide(a):
    return a ^ 0x7f

code = [ BitVec('b%i' % i, 8) for i in range(0, 0x30) ]

for i in range(len(key)):
    c = abs(ord(key[i]))
    res_multiply = (c ^ 0x7f) & 0xff
    res_sub = (res_multiply - 0x42) & 0xff
    res_add = (res_sub ^ 0x69) & 0xff

    s.add(add(code[i], i) == res_add)

assert s.check() == sat

m = s.model()
flag = ''.join([ chr(m[k].as_long()) for k in code ])

# Pour des raisons que j'ignore, la solution de z3 est incorrecte: MCTF{D0_n0T_TrU$T_7h3_S\x88Mb0m5,_Tg3rjAs3bL\x841kGx\x00
# et ne passe pas si l'on applique une contrainte sur le charset
# On va donc bruteforce ce qu'il reste

flag = flag[:23]

for i in range(len(flag), len(key)):
    for c in printable:
        res = add(ord(c), i)
        res = sub(0x69, res)
        res = multiply(res, 0x42)
        res = divide(res) % 0x100

        if res == ord(key[i]):
            flag += c
            break 

print(flag)https://voydstack.fr/writeups/keychecker
```

# Flag
`MCTF{Th3_Al4Rm_Ha$_G0n3_0ff}`
