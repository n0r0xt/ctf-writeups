# MultiVerse Writeup  

## this is my writeup for the MultiVerse OSINT challenge from 0xFun CTF 2026 



A username Massive-Equipment393 is given, and when searching it, I found an associated Reddit account: https://www.reddit.com/user/Massive-Equipment393/

In that account, there was a post that stood out, titled playlist and the following text: 


```
49Rak48kGp7nJoUq9ofCX
```
Which decodes from Base58 to:

```
pl4yl1st_3xt3nd
```

Which I assume is part of the flag. 

The title of the post was playlist, so I next looked at the linked Spotify account. One of the playlists descriptions was the string:  

```
MHhmdW57c3AwdDFmeV8=
```
Which decodes from Base64 to:

```
0xfun{sp0t1fy_
```

Knowing that this is the start of the flag, I tried submitting 0xfun{sp0t1fy_pl4yl1st_3xt3nd} as the flag but it was not correct, so I knew there was more.  

After looking at the spotify account for longer, I noticed that the other playlist was unusual, containing 2 of the same song, both starting with "_". After seeing what the rest of the playlist songs started with, I knew it was the last part of the flag: _m0r3_tr4x}  

So putting them all together made the final flag: 0xfun{sp0t1fy_pl4yl1st_3xt3nd_m0r3_tr4x}