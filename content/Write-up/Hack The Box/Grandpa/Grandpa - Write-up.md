Enumeração de serviços:
![[Write-up/Hack The Box/Grandpa/img/Pasted image 20240229191104.png]]
Durante enumeração dos diretórios, descobrimos que existe o Frontpage Server Extension rodando no servidor:
![[Write-up/Hack The Box/Grandpa/img/Pasted image 20240229191934.png]]
Com algumas pesquisas, identificamos um exploit publico e ao ser executa, pegamos a shell do alvo:
![[Write-up/Hack The Box/Grandpa/img/Pasted image 20240229192014.png]]
Ao verificar que o alvo possui Windows Server 2003, executamos o exploit churrasco, pra enviar uma shell reversa com permissão de root da máquina:
![[Write-up/Hack The Box/Grandpa/img/Pasted image 20240229192530.png]]