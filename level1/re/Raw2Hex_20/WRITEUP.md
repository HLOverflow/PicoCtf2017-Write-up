# Raw2Hex [ 20 Pts ]

## Problem

This program just prints a flag in raw form. All we need to do is convert the output to hex and we have it! CLI yourself to /problems/900be7006255006d8d4e09164dba63c0 and turn that Raw2Hex!

## Approach

xxd or hexdump is a good program.

I particularly like xxd because it can do seek and output just the hex alone. You can even exfiltrate binary files out via copy paste and reverse back to binary very easily.

```
XXXXXXX@shell-web:/problems/900be7006255006d8d4e09164dba63c0$ ./raw2hex | xxd
0000000: 5468 6520 666c 6167 2069 733a 31a3 30cd  The flag is:1.0.           
0000010: 93cd 9f0e fbf1 44d9 3036 eded            ......D.06..               

XXXXXXX@shell-web:/problems/900be7006255006d8d4e09164dba63c0$ ./raw2hex| xxd -s 12 -ps                                                                  
31a330cd93cd9f0efbf144d93036eded                                             
```

(Completed)
