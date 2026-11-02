# UART Writeup  



## this is my writeup for the UART challenge from 0xFun CTF 2026 



Provided is a raw analog capture file of some serial communication.  



I opened it in sigrok/pulseview and assuming based on the challenge name, used the UART decoder to decode it, which made it into the following hex values:  



```

30 78 66 75 6E 7B 55 41 52 54 5F 38 32 5F 4D 32 5F 42 33 39 32 6E 39 64 6E 32 7D

```



I then used cyberchef to decode the hex to get the flag: 0xfun{UART\_82\_M2\_B392n9dn2}

