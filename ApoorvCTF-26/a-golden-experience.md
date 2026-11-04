# A Golden Experience Writeup
## this is my writeup for the A Golden Experience Revsersing challenge from ApoorvCTF 2026

Provided is a Rust PIE ELF. When running it, It says "loading flag" and "printing flag...." and then exits.  

Looking at strings and imports, there was nothing obvious either.

There is no symbols and it is rust, so I disassembled and started from entry0 to find main.  

After finding main, I found a loop with xor and a hardcoded 0x5a key that takes values and adds them to a vec<> which is rust's equivalent of a dynamically sized array: 

```
xor    reg, 0x5a
```

I quickly ran it under a debugger to see where the reg values were coming from, and I saw it was 45 bytes from .rodata:  

```
3B2A3535282C392E3C21146A05176A08690508690B0F6B6917056B14050E126B6F0569020A69086B6914196927
```

I copied these bytes and XORed them all with 0x5a which gave the flag: apoorvctf{N0_M0R3_R3QU13M_1N_TH15_3XP3R13NC3}