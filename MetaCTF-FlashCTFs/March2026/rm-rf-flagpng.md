\# RM -RF Flag.png Writeup

\## this is my writeup for the RM -RF Flag.png forensics challenge from MetaCTF March 2026 Flash CTF  



A filesystem image file is provided: flash.img  



This challenge was quite simple because it was just an image of a filesystem. I first used fls to get the metadata address:  



```shell



fls -r -d flash.img | grep png  



```



Then I used icat with the address from fls to recover the file:



```shell



icat flash.img <address> > flag.png



```  









