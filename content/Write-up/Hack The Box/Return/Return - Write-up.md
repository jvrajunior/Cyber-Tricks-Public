Enumeração de serviços:
![[Write-up/Hack The Box/Return/img/Pasted image 20240227224130.png]]
Analisando o site, encontramos uma página de configuração com informações do servidor, porta, usuário e senha.
![[Write-up/Hack The Box/Return/img/Pasted image 20240227231925.png]]
Ao enviar a requisição, notamos que o único parâmetro enviado é o IP:
![[Write-up/Hack The Box/Return/img/Pasted image 20240227232004.png]]
Vamos então trocar pelo IP da máquina atacante e escutar a porta 389, conforme indicado na página de configuração:
![[Write-up/Hack The Box/Return/img/Pasted image 20240227232110.png]]
Dessa forma recebemos o que parece ser a senha do usuário:
```
svc-printer:1edFg43012!!
```
Com as credenciais descobertas, podemos conectar via winRM na porta 5985:
```bash
evil-winrm -u svc-printer -p '1edFg43012!!' -i 10.10.11.108
```
![[Write-up/Hack The Box/Return/img/Pasted image 20240228083957.png]]
Ao realizar a enumeração dos grupos que o usuário pertence, verificamos que o mesmo está no grupo `Print operators` que permite iniciar e para serviços do Windows.
![[Write-up/Hack The Box/Return/img/Pasted image 20240228111716.png]]
Vamos então subir um netcat para o alvo e alterar o caminho de inicialização do serviço vss para o netcat, incluindo o envio da shell do alvo para a máquina atacante e iniciar o serviço.
![[Write-up/Hack The Box/Return/img/Pasted image 20240228112232.png]]

