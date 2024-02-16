Enumeração dos serviços:
![[Pasted image 20240201103307.png]]

Adicionamos `contacttracer.thm` ao arquivo `/etc/hosts`
Enumeração de diretórios e arquivos:
![[Pasted image 20240201105633.png]]
![[Pasted image 20240201111455.png]]

Bypass na tela de login utilizando SQLi: `' OR 1=1#`.
Depois de logado, podemos subir uma shell reversa em PHP na imagem da logo da empresa. Após o upload é só deslogar e acessar a home novamente.
![[Pasted image 20240204222859.png]]

DB:
`cts:YOUMKtIXoRjFgMqDJ3WR799tvq2UdNWE`

Extraindo o hash do admin do MySQL:
`mysql -u cts -e 'use cts_db;select * from users;' -p`

Hash:
`admin:3eba6f73c19818c36ba8fea761a3ce6d`

Comando para quebrar hash de senha do MySQL:
`hashcat -m 0 -a 0 hash-admin.txt /usr/share/wordlists/rockyou.txt`

Senha quebrada:
![[Pasted image 20240204224954.png]]
`admin:sweetpandemonium`

Identificando usuários na máquina:
![[Pasted image 20240204225052.png]]

Troca para usuário cyrus utilizando `su` com a senha do admin quebrada anteriormente:

Usuário tem permissão para execução de script como root
![[Pasted image 20240204225559.png]]
Cria um arquivo .yara nas configurações que irá copiar o /etc/shadow.
Quebra da senha utilizando hashcat do usuário maxine:
`maxine:tiarna`
Usuário tem permissão de excutar qualquer binário com sudo:
`sudo su`
