* [Recon-Enumeration](#recon-enumeration)
* [Exploit](#exploit)
* [Privesc](#privesc)
* [Discoveries](#discoveries)

## Recon-Enumeration  
![alt text](./img/oopsie01.png?raw=true)  
![alt text](./img/oopsie02.png?raw=true)  
Let’s check the page‘s source code  
![alt text](./img/oopsie03.png?raw=true)  

Hummmm, this sounds interesting. Let’s try to access the url ../cdn-cgi/login  
![alt text](./img/oopsie04.png?raw=true)  

Ok, it’s a login page. However we don’t have a clue about the credential.
Since this website is from MegaCorp (MegaCorp Automotive), maybe the credential found in the ARCHETYPE machine works here  
administrator:MEGA*********.....FAIL!!!  

Hummmm this credential is not working….. maybe admin:MEGA*******.....SUCCESS!!

## Exploit  
![alt text](./img/oopsie05.png?raw=true)  

Hummm we have an Upload option….. maybe we can upload a php reverse shell page to the server. Let’s try...  
![alt text](./img/oopsie06.png?raw=true)  

….. super admin? WTF?….. ok ok, let’s chill out. Look’s like we need a higher privilege user to do that. Let’s check the other options…….that ACCOUNT option in the Menu looks curious, let’s check it  
![alt text](./img/oopsie07.png?raw=true)  

Ok, the ID param in the url got my attention. Let’s try to change it  
Trying change it to 2 or 3 didn't work, but with 4…...  
![alt text](./img/oopsie08.png?raw=true)  

It looks like we can obtain all the user from the server just changing the ID param…. we can do this manually, but why not use a script?  
Let’s get some information first…..  
First, the server identify the user logged with 2 cookies  
![alt text](./img/oopsie09.png?raw=true)  

Those are identical to the ACCESS ID and NAME in the page with ID=1. So maybe we can change the user changing those values to the values of other user… let’s continue our investigation  
When we set an ID that has an user the important line in the source code is this  
![alt text](./img/oopsie10.png?raw=true)  

If we set an ID that doesn’t has an user….  
![alt text](./img/oopsie11.png?raw=true)  

Ok, so we can filter only the line WITH “Access ID” but WITHOUT “<td></td>”  
With that information we can use CURL inside a for loop to get the information…  
![alt text](./img/oopsie12.png?raw=true)  

Now let’s try to change the cookies and refresh the upload page  
![alt text](./img/oopsie13.png?raw=true)  
TOUCHE!  

Now let’s create an php reverse shell, i like to use the one that come with KALI LINUX  
![alt text](./img/oopsie14.png?raw=true)  

Don’t forget to change de IP inside the .php file and set the netcat listener  
Upload the file  
Remmember we found an /uploads/ subfolder? Well our file go to there. So if we access it…  
![alt text](./img/oopsie15.png?raw=true)  
![alt text](./img/oopsie16.png?raw=true)  
HABEMUS SHELL \o/  

## Privesc  

Ok, we have www-data user….let’s try to find the first flag in /home  
![alt text](./img/oopsie17.png?raw=true)  

Now let’s try to escalate or privileges  
First let’s run the command: find / -perm -u=s 2>/dev/null  
![alt text](./img/oopsie18.png?raw=true)  
 
Almost everything is “default”, but that bugtracker is suspicious. Let’s run it  
![alt text](./img/oopsie19.png?raw=true)  

Dammmmm, ok. So let’s continue our enumeration…  
Let’s see if the website has some page/file that we haven’t found  
![alt text](./img/oopsie20.png?raw=true)  

Oops, db.php? That file can be usefull, let’s read it  
![alt text](./img/oopsie21.png?raw=true)  

Great, another credential. So let’s try to use it  
![alt text](./img/oopsie22.png?raw=true)  

It worked….but this shell…. maybe this cred work in the port 22 (ssh) too. Let’s try it…  
![alt text](./img/oopsie23.png?raw=true)  

Much better, now let’s try that bugtracker again...  
![alt text](./img/oopsie24.png?raw=true)  

Looks like this app make some research and show the output, but that “Synchronized browsing is disabled” can indicate that it’s offline... so where he search for the ID we input? Maybe strings can help us with that…  
![alt text](./img/oopsie25.png?raw=true)  

Ok, so it run the cat command. However it doesn’t set the path, so we can create an script with the name "cat" and the "/bin/bash" command inside. When the bugtracker be runned it will run the /bin/bash with root privilege, giving us a root shell….. let’s try it  
![alt text](./img/oopsie26.png?raw=true)  

So if we run bugtracker now it will execute this script in /tmp with root privilege and should open a root shell  
![alt text](./img/oopsie27.png?raw=true)  
PWNED!!!  

Now don't forget to remove the /tmp folder from the PATH to use the correct cat command ;D  
export PATH = /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin  

## Discoveries  
