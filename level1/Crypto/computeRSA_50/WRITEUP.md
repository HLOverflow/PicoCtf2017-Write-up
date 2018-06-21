# computeRSA [ 50 Pts ]

## Problem

RSA encryption/decryption is based on a formula that anyone can find and use, as long as they know the values to plug in. Given the encrypted number 150815, d = 1941, and N = 435979, what is the decrypted number?

## Approach

This is just calculation of numbers.

RSA formula 
for encryption:  c = m^e (mod N)
for decryption:  m = c^d (mod N)

```
Python 2.7.12 (default, Dec  4 2017, 14:50:18) 
[GCC 5.4.0 20160609] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> c=150815
>>> d=1941
>>> N=435979
>>> pow(c,d,N)
133337
```

The flag is 133337

(Completed)

