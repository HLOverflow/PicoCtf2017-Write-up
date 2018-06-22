# Programmers Assemble [ 75 Pts ]

## Problem

You found a [text](./assembly.s) file with some really low level code. Some value at the beginning has been X'ed out. Can you figure out what should be there, to make main return the value 0x1? Submit the answer as a hexidecimal number, with no extraneous 0s. For example, the decimal number 2015 would be submitted as 0x7df, not 0x000007df

## Approach

```
.global main

main:
    mov $XXXXXXX, %eax
    mov $0, %ebx
    mov $0x8, %ecx
loop:
    test %eax, %eax
    jz fin
    add %ecx, %ebx
    dec %eax
    jmp loop
fin:
    cmp $0xb790, %ebx
    je good
    mov $0, %eax
    jmp end
good:
    mov $1, %eax
end:
    ret
```

Let us first rewrite the above snippets into an easier to understand language since this is what reverse engineering is about.

```python
def main():
	a = ?
	b = 0
	c = 8
	while(a!=0):
		b = b+c
		a -= 1
	if b == 0xb790:
		return 1
	else:
		return 0
```

From the above code, we can infer that we are to find the number of iterations of the while loop.

This can be calulcated by 0xb790 / 8 = 5874 = 0x16f2

The flag is 0x16f2

## Verification

The flag can be verified by replacing the line 4 of the given source code and compile with `gcc assembly.s -o test`

The return value can be obtained via `echo $?` which will print out the return code of the program.


(Completed)
