# Dead Drop Writeup
## this is my writeup for the Dead Drop Forensics challenge from MetaCTF February 2026  

Provided is a pcap file, and the context that something was being exfiltrated.  

Looking at the HTTP streams, I see that a user uploaded a file flag.png to a web server.  

To get this, I used Wireshark's HTTP objects feature to export the PNG and read the flag from the preview of the image.    


Flag: MetaCTF{dr0p_d34d_g0rg30us_f0rr3ns1cs_4b1li7y}

