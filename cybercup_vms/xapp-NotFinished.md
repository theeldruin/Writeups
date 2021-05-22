* [Recon-Enumeration](#recon-enumeration)
* [Exploit](#exploit)
* [Privesc](#privesc)

![alt text](./img/xapp00.PNG?raw=true)  

## Recon-Enumeration  
nmap -A -p- -Pn 10.2.0.14  
![alt text](./img/xapp01.PNG?raw=true)  
  
![alt text](./img/xapp02.PNG?raw=true)  
  
gobuster dir -u http://10.2.0.14 -w ~/wordlists/big.txt -e -x php,html,htm,txt,xml -t 100 --no-error  
![alt text](./img/xapp03.PNG?raw=true)  
  
![alt text](./img/xapp04.PNG?raw=true)  
  
![alt text](./img/xapp05.PNG?raw=true)  

## Exploit
Ok, let’s try connect with ftp  
![alt text](./img/xapp06.PNG?raw=true)  

Damm…. maybe SMB…  
![alt text](./img/xapp07.PNG?raw=true)  

Ok, on port 5358 we have HFS HttpFileServer 2.3 - Let’s try to find a exploit to it
searchsploit hfs file server 2.3  
![alt text](./img/xapp08.PNG?raw=true)  

The FIRST and SECOND are interesting. Let’s read the first
searchsploit -x windows/remote/49584.py  
![alt text](./img/xapp09.PNG?raw=true)  
  
Reading the code we discover it use a similar syntax with metasploit. It mount an powershell reverse shell command, encoded it on base64, send it to the target and start a netcat listener to receive the reverse shell.

So let’s adjust those variables and try to run the code
searchsploit -m windows/remote/49584.py
subl 49584.py (you can use nano too)  
![alt text](./img/xapp10.PNG?raw=true)  

lhost = your IP
lport = your PORT, where you will receive the reverse shell
rhost = target host
rport = target port

Everything set, let’s run it with “python3 49584.py”  
![alt text](./img/xapp11.PNG?raw=true)  
  
HABEMUS SHELL \o/

After some enumeration the first flag is on C:\users\public  
![alt text](./img/xapp12.PNG?raw=true)  

net user  
![alt text](./img/xapp13.PNG?raw=true)  

net user pwk  
![alt text](./img/xapp14.PNG?raw=true)  

## Privesc
