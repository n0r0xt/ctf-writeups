# VMWare Writeup  

## this is my writeup for the VMWare Forensics challenge from 0xFun CTF 2026  





Provided is a VMware virtual machine directory for a kali machine.  



When opening it in VMware, the description shows a username kali but no password, and the objective is to log in and find the flag.  



Because Kali uses systemd and GRUB, I used something I found called Linux Emergency Recovery. I first entered GRUB, then pressed e to edit the boot configuration, and replaced the parameter in the Linux section with  "init=bin/bash"  



This made it so that when booting into the OS, before loading systemd it instead launches a root shell which can be used to reset the password of the kali user: 



```shell



#passwd kali



```



After this, I rebooted into Kali normally and used the new password to log in and found the flag in the root directory. Flag: 0xfun{w1th0ut_p2ssw0rd_1s_cr4zy_a2_h3ll}



