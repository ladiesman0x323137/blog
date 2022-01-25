[Back](PicoFrontPage.md)

#Crackmes
---

## 4N0NY31TY's First Crackme
Author: 4N0NY31TY
Difficulty: 1.4/5
Platform: Unix/Linux etc.

### Tools

* Pwntools - Python
* Disassembler - Cutter

### Analysis

To piece everything together, I can assume that the source code would look something like this:

```c
char* input() {
    allocate memory
    read characters and add to var_19h
    reallocate memory if greater than size
    once new line is read, exit and return ptr
}

int len(char* arg1) {
    loop through arg1 and calculate length
    return length
}

int main() {
    arg1 = input;

    for (int var_ch = 0; var_ch < len(arg1); var_ch++) {
        var_10h = var_10h + arg1[var_ch];
    }

    if (var_10h == len(arg1)*-10 + 0x34e7) {
        printf("License is valid!\n");
    } else {
        printf("License is invalid!\n");
    }
}
```

We need to do the math for this. Find a value that is equal to its `length*-10 + 0x34e7 (13543)`.

If we look at an ASCII table, we can see that the character *z* has a high decimal value of 122.

Through trial and error, I calculated that a value of 102 *z*'s would land very close to `102*-10 + 13543 = 12523`.
From there if we add one more character we could find a value that would be able to complete the input.
I was able to calculate this as the ASCII character *E* and outcome with a final value of ***12513***.

I thought that actually typing in 103 characters would be infeasible, so I used Pwntools to enter it for me.

```python
from pwn import *

p = process('./crackme')

context.arch = "amd64"
c = constants

#PROGNAME = ""
#REMOTE = ""
#REMOTEPORT = 0

#if args.REMOTE:
#    p = remote(REMOTE, REMOTEPORT)
#else:
#    p = process(PROGNAME)

#elf = ELF(PROGNAME)
#libc = ELF("") #elf.libc

# payload
payload = b'z'*102 + b'E'
# len = -10*103 + 0x34e7 = 12513
# var_10h = 122*102 + 1*69 = 12513

p.sendline(payload)

recvmsg = p.recvall()
print(recvmsg)

p.interactive()
```
