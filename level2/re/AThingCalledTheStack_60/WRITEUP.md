# A Thing Called the Stack [ 60 Pts ]

## Problem

A friend was stacking dinner plates, and handed you [this](./assembly.s), saying something about a "stack". Can you find the difference between the value of esp at the end of the code, and the location of the saved return address? Assume a 32 bit system. Submit the answer as a hexidecimal number, with no extraneous 0s. For example, the decimal number 2015 would be submitted as 0x7df, not 0x000007df

## Approach

```
foo:
    pushl %ebp
    mov %esp, %ebp
    pushl %edi
    pushl %esi
    pushl %ebx
    sub $0xf8, %esp
    movl $0x1, (%esp)
    movl $0x2, 0x4(%esp)
    movl $0x3, 0x8(%esp)
    movl $0x4, 0xc(%esp)
```

Each push is a 4.

There are 4 pushl hence 16.

ESP is further subtracted by 0xf8.

The problem is asking the distance between esp and return address.

Total distance = 0xf8 + 16

The flag is 0x108

## Illustration

the stack will look like the following.

|addr offset| value |
|-----|-----|
|ebp-0xf8-12|1|
|ebp-0xf8-8|2|
|ebp-0xf8-4|3|
|ebp-0xf8-0|4|
|...|...|
|ebp-12|ebx|
|ebp-8|esi|
|ebp-4|edi|
|ebp+0|saved ebp|
|ebp+4|retn addr|

(Completed)

