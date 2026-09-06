# License to Rev Writeup

## this is my writeup for the License to Rev challenge from MetaCTF February 2026 Flash CTF

Provided is an ELF file called license\_to\_rev  

When executing the file, the user is prompted for a path to a license file.  

The tool I used detected that there was zip archive data embedded in the ELF so I used  binwalk -e to extract it, which gives a text file license.txt which looked like this:  

```

LICENSE\_TYPE=Professional

SERIAL=MCT-4XHA-GKNA-5F1E

LICENSED\_TO=Terrance Troutt

ISSUE\_DATE=2025-07-01

EXPIRY\_DATE=2026-02-01

ACTIVATION\_ID=MNZHOM7YMY63DQBG1GTW



TERMS=This license is non-transferable. Each copy is individually licensed.



```  

When trying to run license\_to\_rev with a path to this file as an argument it returns an error that the license has expired. To defeat this, I simply just changed my system clock to before the expiry date and then run the program, which returned the flag: MetaCTF{y0u\_g0t\_@\_g0ld3n\_ey3\_4\_r3v}

