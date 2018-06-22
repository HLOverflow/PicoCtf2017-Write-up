# SoRandom [ 75 Pts ]

## Problem

We found sorandom.py running at shell2017.picoctf.com:23741. It seems to be outputting the flag but randomizing all the characters first. Is there anyway to get back the original flag?
Update (text only) 16:16 EST 1 Apr Running python 2 (same version as on the server)

## Approach

```
XXXXXXX: ~$ nc shell2017.picoctf.com 23741
Unguessably Randomized Flag: BNZQ:1l36de9583w5516fv3b8691102224f3e

XXXXXXX: ~$ nc shell2017.picoctf.com 23741
Unguessably Randomized Flag: BNZQ:1l36de9583w5516fv3b8691102224f3e
```

After netcat, We get a random random looking flag but it doesn't change.

```python
#!/usr/bin/python -u
import random,string

flag = "FLAG:"+open("flag", "r").read()[:-1]
encflag = ""
random.seed("random")
for c in flag:
  if c.islower():
    #rotate number around alphabet a random amount
    encflag += chr((ord(c)-ord('a')+random.randrange(0,26))%26 + ord('a'))
  elif c.isupper():
    encflag += chr((ord(c)-ord('A')+random.randrange(0,26))%26 + ord('A'))
  elif c.isdigit():
    encflag += chr((ord(c)-ord('0')+random.randrange(0,10))%10 + ord('0'))
  else:
    encflag += c
print "Unguessably Randomized Flag: "+encflag
```

In line 6 of the provided source code, we can see that the seed is a constant.
This means that the random number generator will always produce the same sequence of pseudorandom numbers from this number.
Therefore, we can slightly modify the script, changing the additions to substractions and we will be able to decrypt the flag.


```python
import random
random.seed("random")

enc = "BNZQ:1l36de9583w5516fv3b8691102224f3e"

dec = ''

for c in enc:
	if c.isupper():
		dec+=chr((ord(c)-ord('A')- random.randrange(0,26))%26 + ord('A'))
	elif c.islower():
		dec+=chr((ord(c)-ord('a')- random.randrange(0,26))%26 + ord('a'))
	elif c.isdigit():
		dec+=chr((ord(c)-ord('0')- random.randrange(0,10))%10 + ord('0'))
	else:
		dec+=c
print dec
```

The flag is FLAG:8a18af7233c1846ad6e1235654851b2d

(Completed)
