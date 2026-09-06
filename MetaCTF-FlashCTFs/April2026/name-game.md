\# Name Game Writeup

\## this is my writeup for the Name Game challenge from MetaCTF April 2026 Flash CTF  



Provided is a packet capture file: capture.pcap  



When looking at the pcap in wireshark, it is full of DNS queries to various services, but one of the domains stood out called totallynotac2.meatctf.com  



There were a few DNS queries to this domain containing hex encoded parts of the flag. When putting them all together and decoding, they made the flag: MetaCTF{dns\_15\_alw4ys\_th3\_culpr1t}

