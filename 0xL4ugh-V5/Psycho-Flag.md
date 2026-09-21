# Psycho Flag Writeup
## this is my writeup for the Psycho Flag reversing challenge from 0xL4ugh V5 CTF  

Provided is an ELF called chall. When running it, it asks for a <flag> argument then says if it is correct or not. Flag format is known to be 0xL4ugh{}

The binary is statically linked and stripped with no imports, so looking for them is not possible.   

When opening in cutter with aaa (auto analysis), I immediately see that it is structured weirdly, using blocks of instructions that compute addresses to jump to, along with lots of junk arithmetic operations as a form of.....

This means that this this binary uses..... and therefore trying to attempt to fully analyse this statically and calculate all the jumps is not feasible.  

So I moved on to dynamic analysis with gdb. Since I want to skip past all the irrelevant instructions and get to where the flag argument is processed, I found the pointer for argv, and set a watchpoint on that address: (add gdb code example with early exit for this)  

I saw that the program runs some code to check the length of it (custom, without calling something like strlen) to check that it is 38 characters long. If it's not, it immediately exits, so I changed my "test flag" to 0xL4ugh{A*29} which is 38 in total.  

The next thing I noticed and where I first really got stuck is that the process calls fork() then always crashes with SIGSEGV at 0x40193f when running under GDB. The instruction there was completely wrong, trying to read from memory that didn't exist.  

The breakthrough for me was when I tried using strace to see what the other syscalls in the program were, which showed me ptrace calls, specifically PTRACE_POKETEXT being made from the parent thread. After a bit of research, I found out that this is a common anti-debugger trick: The child crashes if run alone (like in a debugger), but the parent patches parts of the child's code as they run together (like outside a debugger):  

```
ptrace(PTRACE_POKETEXT, <pid>, 0x401920, 0xaaea944412e289c0) = 0
ptrace(PTRACE_POKETEXT, <pid>, 0x401928, 0x3a61e2faaaaaaaaa) = 0
ptrace(PTRACE_POKETEXT, <pid>, 0x40c5b8, 0x61aaea6f63c299c0) = 0
```

To see what is being patched and where, you can simply just read the arguments. For example, in the first call, the bytes from address 0x401920 are being patched to 0xaaea944412e289c0. Remember that this is in little endian, so in reality they turn out the other way, so for the first one as c0 89 e2, etc.  

So the next thing I decided to do was nop out the fork and ptrace syscalls and vibecode a quick script to apply these patches to the binary. Here's what I got:  

```python

#!/usr/bin/env python3

from elftools.elf.elffile import ELFFile
import struct

PATCHES = {
    0x401920: 0xaaea944412e289c0,
    0x401928: 0x3a61e2faaaaaaaaa,
    0x40c5b8: 0x61aaea6f63c299c0,
}

with open("chall", "r+b") as f:
    elf = ELFFile(f)

    for vaddr, value in PATCHES.items():
        offset = None

        for segment in elf.iter_segments():
            if segment["p_type"] != "PT_LOAD":
                continue

            start = segment["p_vaddr"]
            end = start + segment["p_filesz"]

            if start <= vaddr < end:
                offset = segment["p_offset"] + (vaddr - start)
                break

        if offset is None:
            raise ValueError(f"Could not map VA {vaddr:#x} to file offset")

        data = struct.pack("<Q", value)

        f.seek(offset)
        f.write(data)

        print(f"{vaddr:#x} -> file offset {offset:#x}: {data.hex(' ')}")

```

Now that the patches are applied, I have a binary which can be debugged and I just need to figure out the encryption and get the flag.  

So the next thing I looked for was some sort of comparison and work backwards from there. I found the comparison at 0x406270, with an instruction "repz cmpsb". This compares 2 buffers, given 2 addresses and a buffer size to compare from each one. I set a breakpoint with gdb to find the addresses and buffer size:  

```gdb
(gdb) b *0x406270
(gdb) run 0xL4ugh{AAAAAAAAAAAAAAAAAAAAAAAAAAAAA}
Thread 1 "chall_patched" hit Breakpoint 1, 0x0000000000406270 in ?? ()
(gdb) i r rsi rdi rcx
rsi            0x10000200            268435968
rdi            0x10000064            268435556
rcx            0x26            38

```

So this is comparing 38 bytes of data between 0x10000200 (transformed input) and 0x10000064 (encrypted data). What I noticed though is that when my breakpoint hit, my input of 0xL4ugh{AAAAAAAAAAAAAAAAAAAAAAAAAAAAA} had actually transformed into the bytes: 

```
d9 91 0d b5 a4 8e c1 92 48 48 48 48 48 48 48 48 48 48 48 48 48 48 48 48 48 48 48 48 48 48 48 48 48 48 48 48 48 8c
```

Notice that all the As had become 0x48. This means that it is deterministic. The same input character transforms into the same encrypted one, irrespective of position. So to solve this, all I had to do was give inputs like 0xL4ugh{ABCDEF...0123456...+-/^%_...} to get a list of transformed bytes for each character and find which ones match the flag bytes.  

After doing this, I got the flag: 0xL4ugh{P5ych0_Flag_Hid3s_In_The_Gat3}








