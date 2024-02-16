Enumeração dos serviços:
![[Pasted image 20240129170715.png]]

Wordpress 4.1.31
`wpscan --url $RHOST`
![[Pasted image 20240129171109.png]]
Descoberta de usuários:
![[Pasted image 20240130082337.png]]
Brute force:
![[Pasted image 20240130082405.png]]
c0ldd:9876543210

Login no Dashboard do wordpress e shell reversa através da página 404.

Com a shell, fizemos uma enumeração com o linpeas e encontramos as credenciais do banco de dados do Wordpress:
![[Write-up/Try Hack Me/ColddBox/img/Pasted image 20240130085013.png]]
E obtemos sucesso ao utilizar a mesma credencial para o usuário do sistema:
![[Write-up/Try Hack Me/ColddBox/img/Pasted image 20240130085102.png]]
Pesquisamos pelos binários com permissão SUID e encontramos o find. Podemos fazer a escalação de privilégio:
![[Write-up/Try Hack Me/ColddBox/img/Pasted image 20240130085249.png]]