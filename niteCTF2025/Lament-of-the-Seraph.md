# Lament of the Seraph Writeup
## this is my writeup for the Lament of the Seraph malware challenge from niteCTF 2025   

(not fully solved during the CTF timeframe, so I took a look at the source code afterwards to finish solving and complete this writeup)

This was a big step up in difficulty compared to what I had done before, but I put a good few hours into it to see how far I could get. 

Provided is a 32-bit PE: seraphs_lance.exe  

The context is to do with crypto wallets getting locked by ransomware and "once placing trust in a public server"  

I started with static analysis, trying to determine a "main" function and understand the control flow of the program.  

I identified this to be sub_402A22, which has numerous function calls and contains the ransom note which tells that the program is a ransomware which encrypts crypto wallets. This usually means that the way to get the flag is something to do with the cryptography used.  

The first function that is called by this "main" is sub_401D18, which does a couple of simple debugger checks but nothing else, so I decided just to nop it out.  

I noticed that the next function, sub_401E0E, had some strings like "kernel32.dll" in function calls for sub_4019B1, but twice with 2 different parameters: 0xA88138FB and then 0x49BEDE3. This is important because the PE itself had minimal imports in the IAT, so this could be a clue as to how it resolves its function calls. 

I then looked at sub_4019B1, which goes through the export directory of the PE header and resolves some function in kernel32.dll by comparing the provided value, 0xA88138FB and 0x49BEDE3 in this case, against the result of running a hashing algorithm sub_4014E0 on each function name it parses.  

I tried to understand the hashing algorithm implemented in sub_4014E0 but I couldn't. So after some manual debugging, I found out that the two kernel32.dll functions being resolved and called were IsWow64Process and GetCurrentProcess.  

After this, I learned a bit about Wow64 and a related technique called "Heaven's Gate". Wow64 is what lets the 32-bit process run on 64-bit windows, so a Wow64 process can transition between both 32 and 64-bit execution and therefore use both APIs. A common pattern for this is searching the 64-bit PEB for the 64-bit ntdll and resolving the system32 functions manually, so I assume something similar is happening here.  

The next function that caught my attention was sub_401FD1 because of the string "%s\\AppData\\Roaming\\Electrum\\recent_servers". I know that electrum is a bitcoin wallet, and after some further research, I found that this is a JSON file that stores the details of recent public servers that have been used by electrum. The function first gets the USERPROFILE using the PEB to build that full path then accesses the recent_servers file.   

The function then looks for a string in the file then copies it, hashes it with MD5, compares the hash value with "5fc44255053d10f73c65104fa689843f", then copies it out if it matches and uses it later. This is where the clue about "placing trust in a public server" comes in. I didn't get this, but the intended solution was to look up  the addresses of some common public Electrum servers. In this case, it was "electrum.blockstream.info" that matched.  

The next piece of the puzzle is the encryption itself, which wasn't too hard to understand conceptually. In the function sub_402531 there were 2 strings, "%s\\AppData\\Roaming\\Electrum\\wallets" which is the location of Electrum wallets, and "openssl enc -aes-256-cbc -in \"%s\" -out \"%s.seraph\" -K %s -iv 534552415048535F4C414E43455F3031 -nosalt" which is a command that encrypts the file given with AES-256-CBC and gives it the ".seraph" extension. The IV is constant and known because it is in the command, which hints that the key may be an important part of the solution or even the flag itsself. The 32 byte key comes from sub_401E9D, which takes the string "electrum.blockstream.info" that we found earlier as a parameter.  

So I next looked at sub_401E9D which uses the Windows API function VirtualProtect, which lets a process change the permissions of some of its mapped memory, namely its read, write and execution permissions. This is commonly used to make a payload (usually shellcode) executable once it has been loaded into memory.   

The start of the program sets up an exception handler to resume after an exception with the program's instruction pointer at code to patching and running the shellcode and then deliberately crashes during execution to trigger that exception handler.

In this case, the shellcode is structured as some 32-bit instructions to set it up, then a retf (far return) to the actual logic of it which exists in 64-bit (use of the heaven's gate technique I mentioned before). Between these is 32 bytes of data. Remember that the AES encryption key is also 32 bytes, so this could be significant.

The main part of the shellcode is a loop that performs a bitwise xor using that string "electrum.blockstream.info" as a repeating key and those 32 middle bytes: "0B 05 11 06 0F 31 27 34 7E 36 5C 2D 31 5B 20 1A 46 2B 26 5E 62 3A 5A 12 3B 2D 5F 02 57 00 41 08  

I used cyberchef to do this xor and got the Flag (which is also the AES encryption key): nite{CRYPT0BR0Sn4NG3LS4tTH3g4t3}