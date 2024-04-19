Enumeração dos serviços:
![[Write-up/Hack The Box/Optimum/img/Pasted image 20240225152126.png]]

Foi identificado o serviço de compartilhamento de arquivos **HFS 2.3** rodando no alvo.
![[Write-up/Hack The Box/Optimum/img/Pasted image 20240225155821.png]]

Com uma pesquisa no Google, identificamos um exploit público para essa versão do servidor.
https://vk9-sec.com/hfs-code-execution-cve-2014-6287/

Obtemos a shell no host:
![[Write-up/Hack The Box/Optimum/img/Pasted image 20240225163449.png]]

Ao rodar o winpeas.exe encontramos as credenciais do usuários kostas:
![[Write-up/Hack The Box/Optimum/img/Pasted image 20240225172650.png]]
`kostas:kdeEjDowkS*`

Vamos executar o Sherlock em busca de vulnerabilidades conhecidas no Kernel:
![[Write-up/Hack The Box/Optimum/img/Pasted image 20240225175351.png]]
Exploit MS16-032 é necessário PS ou MS16-098 necessário Prompt.
