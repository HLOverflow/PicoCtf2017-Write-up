# Hash 101 [ 50 Pts ]

## Problem

Prove your knowledge of hashes and claim a flag as your prize! Connect to the service at shell2017.picoctf.com:15412
UPDATED 16:12 EST 1 Apr.

## Approach

Write a python script to automate the first 3 level and use crackstation for the last level.

```python
#!/usr/bin/env python

import socket
import re
import time

def answer(sock, result):
	print result
	sock.send(result + "\n")
	time.sleep(1)
	data = sock.recv(2048)
	print data
	return data

level1=re.compile('''ASCII representation of ([0-1]+)''')
level3=re.compile('''give me a string that will result in a ([0-9]+) after being transformed''')

s = socket.socket()
s.connect(('shell2017.picoctf.com', 17428))

data = s.recv(2048)
print data
data = s.recv(2048)
print data

# convert from binary to ascii
b = level1.findall(data)[0]
word = ''
for i in range(0, len(b), 8):
	word += chr(int(b[i:i+8], base=2))

# level 1
answer(s, word)

# level 2
result = word.encode('hex')
answer(s, result)

result = 'K'
answer(s, result)

result = str(int(b, base=2))
data = answer(s, result)

# level 3 - 
remainder = level3.findall(data)[0]
result = chr(int(remainder) + 16*5)
answer = answer(s, result)

# level 4 - use crackstation's help
result = raw_input("crack: ")			# stop automation
s.send(result + "\n")

# receive flag
time.sleep(1)
data = s.recv(2048)
print data

s.close()
```

## Outputs

`
Welcome to Hashes 101!

There are 4 Levels. Complete all and receive a prize!



-------- LEVEL 1: Text = just 1's and 0's --------
All text can be represented by numbers. To see how different letters translate to numbers, go to http://www.asciitable.com/

TO UNLOCK NEXT LEVEL, give me the ASCII representation of 011011000110111101110110011001010110110001111001

>
lovely
Correct! Completed level 1

------ LEVEL 2: Numbers can be base ANYTHING -----
Numbers can be represented many ways. A popular way to represent computer data is in base 16 or 'hex' since it lines up with bytes very well (2 hex characters = 8 binary bits). Other formats include base64, binary, and just regular base10 (decimal)! In a way, that ascii chart represents a system where all text can be seen as "base128" (not including the Extended ASCII codes)

TO UNLOCK NEXT LEVEL, give me the text you just decoded, lovely, as its hex equivalent, and then the decimal equivalent of that hex number ("foo" -> 666f6f -> 6713199)

hex>
6c6f76656c79
Good job! 6c6f76656c79 to ASCII -> lovely is lovely
Now decimal
dec>
K
input a decimal (base 10) number. Only numbers.
dec>
119225983528057
Good job! 119225983528057 to Hex -> 6c6f76656c79 to ASCII -> lovely is lovely
Correct! Completed level 2

----------- LEVEL 3: Hashing Function ------------
A Hashing Function intakes any data of any size and irreversibly transforms it to a fixed length number. For example, a simple Hashing Function could be to add up the sum of all the values of all the bytes in the data and get the remainder after dividing by 16 (modulus 16)

TO UNLOCK NEXT LEVEL, give me a string that will result in a 14 after being transformed with the mentioned example hashing function

>
^
Correct! Completed level 3

--------------- LEVEL 4: Real Hash ---------------
A real Hashing Function is used for many things. This can include checking to ensure a file has not been changed (its hash value would change if any part of it is changed). An important use of hashes is for storing passwords because a Hashing Function cannot be reversed to find the initial data. Therefore if someone steals the hashes, they must try many different inputs to see if they can "crack" it to find what password yields the same hash. Normally, this is too much work (if the password is long enough). But many times, people's passwords are easy to guess... Brute forcing this hash yourself is not a good idea, but there is a strong possibility that, if the password is weak, this hash has been cracked by someone before. Try looking for websites that have stored already cracked hashes.

TO CLAIM YOUR PRIZE, give me the string password that will result in this MD5 hash (MD5, like most hashes, are represented as hex digits):
8b1660cfc5ce8217cb9188cc6b652e91

>
crack: p4p3r
Correct! Completed level 4
You completed all 4 levels! Here is your prize: 8b95d8e7ccd0e41b8f989195443a9072
`

(Completed)
