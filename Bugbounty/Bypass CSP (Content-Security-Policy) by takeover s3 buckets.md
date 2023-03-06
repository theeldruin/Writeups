Olá pessoal, aqui vamos em um breve passo-a-passo de ocmo descobri essa vulnerabilidade em um programa de bugbounty.

# Recon
Como sempre iniciamos fazendo um reconhecimento do dominio disponibilizado. Geralmente utilizo uma combinação de ferramentas para identificar os subdominios(gau,chaos,assetfinder,subfinder,google dorks, etc) e depois jogo tudo no burp para fazer um crawl passivo.

Depois de identificar todos os subdominios procurei pelos arquivos robots.txt. Acessando consegui encontrar alguns diretórios interessantes, mas o que REALMENTE chamou minha atenção tinha sido o tamanho do cabeçalho(Header) de um deles.

# Identificando a vulnerabildiade
Parei para ler e ver o porque de ser tão grande, identifiquei que essa resposta tinha 3 cabeçalhos de políticas de segurança (CSP). Os 3 eram iguais mas nas URLs dentro deles achei alguns links s3
![](Writeups/Bugbounty/images/Pasted%20image%2020230306100240.png)

Extrai eles para um arquivo teste.txt e utilizei o nuclei para testar por subdomain takeover
![](Writeups/Bugbounty/images/Pasted%20image%2020230306100402.png)

Acessei os links para confirmar novamente que estavam disponíveis
![](Writeups/Bugbounty/images/Pasted%20image%2020230306100642.png)

![](Writeups/Bugbounty/images/Pasted%20image%2020230306100721.png)

# Exploração
O restante da exploração foi simples, simplesmente loguei em uma conta no Amazon AWS e criei 2 buckets com os mesmos nomes encontrados anteriormente.

Adicionei neles um arquivo poc.html e VOILÁ
![](Writeups/Bugbounty/images/Pasted%20image%2020230306100932.png)

![](Writeups/Bugbounty/images/Pasted%20image%2020230306100954.png)

# Report
Com essas evidências reportei meus achados na plataforma onde a mesma foi aceita.
![](Writeups/Bugbounty/images/Pasted%20image%2020230306175140.png)

