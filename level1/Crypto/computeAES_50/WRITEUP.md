# computeAES [ 50 Pts ]

## Problem

You found this [clue](./clue.txt) laying around. Can you decrypt it?

## Approach

The problem is simple since the key is provided.

```python
#!/usr/bin/env python
from Crypto.Cipher import AES

cipher = "t1h0qbcOhRQF5E46bsNLimfbcI6egrKP4LHtKR3lT4UdWjhssM8RQSBT7S/8rcRy".decode('base64')
key = "T5uVzYtuBNv6vwjohslV4w==".decode('base64')

aes = AES.new(key, AES.MODE_ECB)
print aes.decrypt(cipher)
```

The flag is flag{do_not_let_machines_win_1e6b4cf4}

(Completed)
