
# Enumeration
└─$ sudo masscan -p 1-65535 --rate=1000 -i tun0 192.168.233.217                                                                                                                                                                               


└─$ sudo nmap -sSCV -p 22,80 -Pn -v 192.168.233.217  
![](Pasted%20image%2020230105144341.png)

└─$ feroxbuster -u http://blogger.thm -w ~/wordlists/big.txt 
![](Pasted%20image%2020230105143401.png)

└─$ feroxbuster -u http://blogger.thm/assets/fonts/blog -w ~/wordlists/big.txt -x php,xml,txt,html,pdf,htm,sql,backup,bkp,bak -n
![](Pasted%20image%2020230105151239.png)

└─$ katana -u http://192.168.98.217 -nc -silent -jc -kf all
![](Pasted%20image%2020230105100226.png)

Explorando os diretorios encontrados
![](Pasted%20image%2020230105101349.png)

![](Pasted%20image%2020230105101418.png)

└─# wpscan --url http://blogger.thm/assets/fonts/blog/ --enumerate u,vp,vt,cb,dbe --usernames 'j@m3s' --passwords /home/eldruin/wordlists/rockyou.txt -t 30     
                                                              



