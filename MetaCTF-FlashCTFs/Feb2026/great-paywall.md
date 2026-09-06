# Great Paywall Writeup
## this is my writeup for the Great Paywall challenge from MetaCTF February 2026  

This is a simple challenge. The flag is in the page, but a popup blocks it. To get the flag, get it from the page source itself. One line solve:  

```shell

curl <url> | grep MetaCTF{  

```

Flag: MetaCTF{p4yw4ll5_4r3_ju5t_d0m}
