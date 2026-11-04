# KameHame-Hack Writeup
## this is my writeup for the KameHame-Hack web challenge from ApoorvCTF 2026  


This a Dragon Ball themed web "game" where you enter a fighter name and it puts you into 3 fights, where you have a "power level" and whoever has a higher one wins. With the game as it is, you always win the first 2 fights but lose the last one, having 70,000 power when the last opponent has 1,000,000.  

I started by taking a look at what actually happens when it runs. For each fight, it assigns a cookie which is a normal flask cookie, containing the base64 encoded JSON payload, a timestamp, and a HMAC signature.  

So I first decoded the payload part of the cookie to understand what was happening, and it contained a few stats like name, stage, power_level, and zenkai_boost (which I assumed multiplies power level). It's not possible to directly modify the cookie payload though, because it is signed by the server when issued.  

The app also uses jinja2 rendering, so SSTI could be a strong possibility because the entered fighter name is rendered and displayed in each fight.  

I next tried different names to see how they are displayed, trying out different characters and url encoding. I noticed that names containing some characters like "'% would return an sql error: 

```
Fatal error: Uncaught mysqli_sql_exception: You have an error in your SQL syntax...
```

This would usually mean that SQL injection is the way to go. However, when looking closer, there was also a hidden message with a hint:  

```
<p style="font-size:1px;color:#0a0a0a">wrong injection type. how does your name get rendered?</p>
```

This hint tells me that the solution is jinja2 SSTI, but now I just need to figure out what to do with it. Since " characters are blocked, I can't use normal RCE methods like globals or subclasses.  

Looking closer at the page source of the main page, there is another hint: 

```
// "A Saiyan's true power is stored within. To surpass a God, one must update() their inner __dict__ionary."
```

In python, update() is a method for updating or adding items to a dictionary and __dict__ is a class attribute which lets you reference the attributes of a class as a dictionary. In this case, there is an instance of the class player, with attributes like power_level.  

Using this hint, it is possible to get the final payload:  

```
{{ player.__dict__.update(power_level=99999999) }}
```

Now, when using this name and getting the last stage, you have more than the 1,000,000 power level needed to win and it returns the flag: apoorvctf{J1nj4_N1nj4_baybay}