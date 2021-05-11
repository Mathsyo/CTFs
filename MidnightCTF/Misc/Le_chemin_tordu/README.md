# Description
Un algorithme qui vous demande des opérations de plus en plus complexes dans un temps imparti, pourriez-vous en venir à bout ?
Accès au challenge : `nc ctf.midnightflag.fr 9000`

# Write UP

## 1
```Python
from pwn import *
from word2number import w2n

r = remote('ctf.midnightflag.fr', 9000)

r.sendline('yes')

while True:
    try:    
        r.recvuntil('What is the result of ')

        eqraw = r.recvline().decode().split(' ?')[0]

        # On évite d'avoir un espace, pour ne pas planter sur le eqraw.split()
        eqraw = eqraw.replace('one hundred', '100')
        
        print(eqraw)
        eq = ''
        for w in eqraw.split():
            if w in '+-*/%':
                eq += w
            else:
                eq += str(w2n.word_to_num(w))
        print(eq)
        if len(eq) > 30:
            input('WARN')

        res = eval(eq)

        r.sendline(str(res))

    except:
        print(r.recv())
```

## 2 
```python
from pwn import *
from num2words import num2words
from string import ascii_lowercase
from re import match

def get_words_to_nums():
    d = dict()
    for i in range(1, 101):
        d[i] = num2words(i, lang="en")
    return d

def phase2(chall):
    m = get_words_to_nums()
    for k, v in list(m.items())[::-1]:
        chall = chall.replace(v, str(k))
    print(chall)
    return chall

if __name__ == '__main__':
    mapping = get_words_to_nums()
    context.log_level = "debug"
    conn = remote("ctf.midnightflag.fr", 9000)
    conn.recvline()
    conn.recvline()
    conn.send(b"yes\n")
    while True:
        conn.recvuntil(b"What is the result of ", drop=True)
        chall = conn.recvline(keepends=False)
        chall = chall.strip()[:-2]
        try:
            chall = phase2(chall.decode("utf-8"))
            res = eval(chall)
            conn.send(f"{res}\n")
        except NameError:
            res = phase2(chall)
            conn.send(f"{res}\n")
```

# Flag
-