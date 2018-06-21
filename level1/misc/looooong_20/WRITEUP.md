# looooong [ 20 Pts ]

## Problem

I heard you have some "delusions of grandeur" about your typing speed. How fast can you go at shell2017.picoctf.com:44840?

## Approach

This is a test of socket programming.

```python
#!/usr/bin/env python
import socket
import re

res = re.compile('''To prove your skills, you must pass this test.
Please give me the '([A-Za-z])' character '([0-9]+)' times, followed by a single '(.)'.
To make things interesting, you have 30 seconds.''')

s = socket.socket()
s.connect(("shell2017.picoctf.com", 41123))
data = s.recv(2048)

print data
print

char, times, end = res.findall(data)[0]

senddata = char * int(times) + end + "\r\n"
print "sending..."
print senddata
print

s.send(senddata)

data = s.recv(2048)
print data

print

s.close()

```

## Output

```
To prove your skills, you must pass this test.
Please give me the 'N' character '728' times, followed by a single '0'.
To make things interesting, you have 30 seconds.
Input:


sending...
NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN0


You got it! You're super quick!
Flag: with_some_recognition_and_training_delusions_become_glimpses_040b5dbaa85682f120e15e7d1224f09c
```

(Completed)
