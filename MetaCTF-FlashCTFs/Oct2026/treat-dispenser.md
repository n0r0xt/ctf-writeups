# Treat Dispenser Writeup
## this is my writeup for the Treat Dispenser Reversing challenge from MetaCTF October 2026 Flash CTF  


Provided is an ELF dispenser that when run, takes a value 1-4 to "give candy", but it says that the "good candy stays locked away", where I assume that good candy is the flag.  

So I start with static analysis, checking the strings and find one containing "KEY=6767" so I run again and enter 6767 as the value and it returns the flag.  

Flag: SkillBit{fu11_s1z3_tr34t_unl0ck3d}