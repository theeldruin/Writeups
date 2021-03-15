Hello, I'm starting my studies on pentest, so I did very badly at this CTF. But here are my notes on the challenges I managed to solve =D

Obs.: I hidded the flags because other CTF writeups that i read. After the end of CTF i discover that i could show the flags #facepalm

* [Veebee](#veebee)
* [easb64](#easb64)
* [Pollex](#pollex)
* [Read the Rules](#read-the-rules)
* [Discord Flags](#discord-flags)
* [#NahamCon2021](#nahamcon2021)
* [The Mission](#the-mission)
  - [Bionic](#bionic)
* [MerchStore](#merchstore)
* [Buzz](#buzz)

## Veebee

Download file and **open**:

270a 270a 2340 7e5e 4841 4141 4141 3d3d
7e2c 507e 2c50 2c50 502c 502c 7e50 2c50
7e50 2c50 7e7e 2c50 502c 7e50 6741 4d41
4141 3d3d 5e23 7e40 0a23 407e 5e71 6741
4141 413d 3d76 2c2e 6e7f 417f 2b50 5457
7f64 5028 4522 792c 343b 222e 4026 4240
2676 4026 742f 6f7e 4761 6072 3f4b 442e
5853 7e09 574f 5044 346c 4450 6e43 6b58
2245 6240 2648 646f 7e47 5860 457d 336d
587e 2c6c 314f 456d 5673 5842 507a 4721
4244 7f7e 4462 6f74 4420 7e71 4450 622f
7e59 3443 4450 6e6c 6b7a 5272 2340 265c
6b6f 4147 6176 4a57 566d 4c50 3030 5a2a
6c2c 2639 2c32 6630 6c63 6626 3620 4354
5730 2152 2b30 5721 2139 305e 3872 2368
6a4d 4141 413d 3d5e 237e 4000

Ok, the bytes “270a 270a” does not represent the beginning of a VBE file:
https://medium.com/@bromiley/malware-monday-vbscript-and-vbe-files-292252c1a16

So let’s remove them and save the file

2340 7e5e 4841 4141 4141 3d3d
7e2c 507e 2c50 2c50 502c 502c 7e50 2c50
7e50 2c50 7e7e 2c50 502c 7e50 6741 4d41
4141 3d3d 5e23 7e40 0a23 407e 5e71 6741
4141 413d 3d76 2c2e 6e7f 417f 2b50 5457
7f64 5028 4522 792c 343b 222e 4026 4240
2676 4026 742f 6f7e 4761 6072 3f4b 442e
5853 7e09 574f 5044 346c 4450 6e43 6b58
2245 6240 2648 646f 7e47 5860 457d 336d
587e 2c6c 314f 456d 5673 5842 507a 4721
4244 7f7e 4462 6f74 4420 7e71 4450 622f
7e59 3443 4450 6e6c 6b7a 5272 2340 265c
6b6f 4147 6176 4a57 566d 4c50 3030 5a2a
6c2c 2639 2c32 6630 6c63 6626 3620 4354
5730 2152 2b30 5721 2139 305e 3872 2368
6a4d 4141 413d 3d5e 237e 4000

Search for a VBE decoder:
https://github.com/DidierStevens/DidierStevensSuite/blob/master/decode-vbe.py

Try to run it:

![alt text](./img/nahamcon2021-1.png?raw=true)

Hummmmm, nothing? Let’s check the hex again….

2340 7e5e 4841 4141 4141 3d3d
7e2c 507e 2c50 2c50 502c 502c 7e50 2c50
7e50 2c50 7e7e 2c50 502c 7e50 6741 4d41
4141 3d3d ***5e23 7e40*** 0a23 407e 5e71 6741
4141 413d 3d76 2c2e 6e7f 417f 2b50 5457
7f64 5028 4522 792c 343b 222e 4026 4240
2676 4026 742f 6f7e 4761 6072 3f4b 442e
5853 7e09 574f 5044 346c 4450 6e43 6b58
2245 6240 2648 646f 7e47 5860 457d 336d
587e 2c6c 314f 456d 5673 5842 507a 4721
4244 7f7e 4462 6f74 4420 7e71 4450 622f
7e59 3443 4450 6e6c 6b7a 5272 2340 265c
6b6f 4147 6176 4a57 566d 4c50 3030 5a2a
6c2c 2639 2c32 6630 6c63 6626 3620 4354
5730 2152 2b30 5721 2139 305e 3872 2368
6a4d 4141 413d 3d5e 237e 4000

Ok, those bytes "***5e23 7e40***" represent the end of the file…. so they are in the wrong place. Let’s remove them and try to run again the decoder

![alt text](./img/nahamcon2021-2.png?raw=true)


## easb64
Download and open the file

![alt text](./img/nahamcon2021-3.png?raw=true)

Looks like a base64 file, let’s open CyberChef an try to decode:

![alt text](./img/nahamcon2021-4.png?raw=true)

Nop….. hummm….. the file name looks inverted. Maybe the code is too. Let’s try to invert it and decode again
https://www.invertexto.com/inverter-texto ---> Website to invert the text

![alt text](./img/nahamcon2021-5.png?raw=true)

Success, just invert the text again and you have the flag


## Pollex
Download the file pollex and open

![alt text](./img/nahamcon2021-6.png?raw=true)

Ok, its a gigantic HEX file. However it started with FFD8, so probably is a JPG file. Let’s try to open it

![alt text](./img/nahamcon2021-7.png?raw=true)

Hummm, maybe there is some hide information in it. Let’s use an EXIFTOOL to analyze:
http://exif.regex.info/exif.cgi

![alt text](./img/nahamcon2021-8.png?raw=true)

Success!


## Read the Rules
Hummm let’s try read the rules od the CTF:
https://ctf.nahamcon.com/rules

Ok… nothing interesting….. maybe the source code reveal something:

![alt text](./img/nahamcon2021-9.png?raw=true)

Ok….that's was easy :D


## Discord Flags
The step showed bellow is the same to hte Challengs: Red-Team-Village,UHC-BR,Live-Recon-Village,IOT-Village,HTB-Village,INE-Carrer-Corner
Just follow the links:

![alt text](./img/nahamcon2021-10.png?raw=true)


## #NahamCon2021
Let’s search the #NahamCon2021+flag in google:

![alt text](./img/nahamcon2021-11.png?raw=true)

## The Mission
Let’s go to THE MISSION page: https://ctf.nahamcon.com/mission
Look the source code:

![alt text](./img/nahamcon2021-12.png?raw=true)

## Bionic
Visite the webpage and check the source….. but is nothing there. Lets use FFUF to search for hide pages:

![alt text](./img/nahamcon2021-13.png?raw=true)

Hummmm robots.txt…. let’s take a look:

![alt text](./img/nahamcon2021-14.png?raw=true)

We got the flag AND a new page

## MerchStore
Let’s follow the link: https://www.nahamcon.com/merch
Look the source:

![alt text](./img/nahamcon2021-15.png?raw=true)


## Buzz
Ok, let’s download the file and give a look at it

![alt text](./img/nahamcon2021-16.png?raw=true)

Ok, another HEX. Let’s “unhex” it:

![alt text](./img/nahamcon2021-17.png?raw=true)

Nothing useful…. let’s search for that bytes 1fd9:

![alt text](./img/nahamcon2021-18.png?raw=true)

we found something about a .Z file. Let’s try to change de file extension to .Z:

![alt text](./img/nahamcon2021-19.png?raw=true)

Unzip doesn’t work, so let’s open the directory and check the file:

![alt text](./img/nahamcon2021-20.png?raw=true)

Inside we found another “buzz” file, let’s open it:

![alt text](./img/nahamcon2021-21.png?raw=true)

Touche! =D
