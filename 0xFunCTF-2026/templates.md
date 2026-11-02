# Templates Writeup  

## this is my writeup for the Templates challenge from 0xFun CTF 2026  

The page is a "Greeting Service", where you enter a name and it "greets" you, which includes a rendered version of the inputted name.  

Based on the name of the challenge, as seeing Jinja2 engine in the response, I try entering {{7*7}}, expecting either it to return the name as 49 or include an error. It output 49, which shows that it was being evaluated as code.  

I used this to check the contents of the current directory and saw a flag.txt file, as well as that the class had access globally to the python os module.  

Here was the final payload to get the flag: 


```
{{ config.__class__.__init__.__globals__['os'].popen('cat flag.txt').read() }}
```

Flag: 0xfun{Server_Side_Template_Injection_Awesome}