# Stacked Logs Writeup
## this is my writeup for the Stacked Logs challenge from MetaCTF April 2026 Flash CTF  



Provided is a log file: server.log  



This is a simple challenge designed to demonstrate that tracebacks from unhandled exceptions in python leak all the values of the local variables in the call stack.  



Solution:  



```shell  



cat server.log | grep MetaCTF  



```



Flag: MetaCTF{unhandl3d\_3xc3pt10ns\_l34k\_s3cr3ts}

