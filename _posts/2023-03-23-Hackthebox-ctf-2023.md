---
title: Hack the Box Cyber Apocalypse CTF 2023
date: 2023-03-23 
categories: [ctf, writeup, htb, jeopardy]
tags: [forensics, hardware, misc, pwn, web, warmup, python, curl]
---

# Hack the Box Cyber Apocalypse 2023 Complete! (≧∇≦)ﾉ

Cyber Apocalypse 2023 was a jeopardy style CTF spanning multiple categories such as: forensics, hardware, pwn, misc, web, machine learning, blockchain and cryptography.

This is my first CTF that I have entered though I continue to complete rooms on TryHackMe, using the HTB Academy and working through the PicoCTF Gym.
I've come to understand during this short experience - there's nothing quite like a live CTF. I hope to continue doing CTFs in future, at least, as much as time allows.

(Will be adding more over time).
- **Posts Added:** trapped source, persistence, plaintext tleasure
- **Posts to follow:** getting started, initialise connection, questionnaire, critical flight, debut, timed transmission, alien cradle and extraterrestrial persistence


## Warmup

-------------------------

This was a fun little flag to be had after joining the Hack the Box discord. It was hiding in the announcements section as:

``` text
HTB{l3t_th3_tr3asur3_hunt1ng_b3g1n!}
```

## Trapped Source (web)
--------------------

This was a pretty straightforward and these were the steps I took for this challenge:

- Viewed the page source (Ctrl + U or Right-Click + View Page Source)
- Scanned the page for any mention of the HTB flag format HTB{...}
- No flag text present, so I checked for any scripts available
- Found a script named "correctPin" and tried the code "8291"
- Got the flag! 


``` text
HTB{V13w_50urc3_c4n_b3_u53ful!!!}
```


## Persistence (misc)
-------------------

First, I'll say, what a fitting name for this challenge 🤣

At the moment I don't know much about web-based challenges and incorrectly approached this at first. I read the question carefully and soon realised that the page needed to be accessed many, *MANY*  times (around 1000 as per the description).

There may be many ways to approach this, but I used what I knew, which was the handy tool called **cURL**.

At first, I tried to simply use the given url with curl, like so:

``` bash
curl ip-address:port/flag
```

And received a garbled string as a result. I knew that it was working, but it wouldn't do to simply repeat this request. So I looked up how to replicate the result continuously.

``` bash
while true; do curl ip-address:port/flag; done 
```

Great! This was working as well, but I needed to save the output somewhere:

``` bash
while true; do curl --output >(cat >> filename) ip-address:port/flag; done 
```

And after some long time later, a flag appears!
We can view it like so, piping it to grep:

``` bash
cat filename | grep "HTB{"
```

Getting the following output:
``` text
HTB{y0u_h4v3_p0w3rfuL_sCr1pt1ng_ab1lit13S!}
```

## Plaintext Tleasure (forensics)
------------------------------------------------------

This challenge had to be downloaded and contained a zip file, which can easily opened with the following command:

``` bash
unzip filename.zip
```

The extract file is named "capture.pcap". which I confirm with the *file* command.
Before searching through the file in its entirety, I decide to try basic commands and see if the flag can be found this way and this is the line which worked:

``` bash
strings capture.pcap | grep "HTB{"
```

