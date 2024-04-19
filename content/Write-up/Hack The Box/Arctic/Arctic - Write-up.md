Enumeração de serviços:
![[Write-up/Hack The Box/Arctic/img/Pasted image 20240303122802.png]]
Navegando pelos diretórios do webserver encontrado na porta 8500, pudemos enumerar que o servidor possui o Adobe Coldfusion 8:
![[Write-up/Hack The Box/Arctic/img/Pasted image 20240303143807.png]]
Pesquisando por exploits público, encontrei um RCE que já envia a shell para o atacante:
https://www.exploit-db.com/exploits/50057
![[Write-up/Hack The Box/Arctic/img/Pasted image 20240303144517.png]]
Após algumas pesquisas, foi possível identificar que o sistema estava vulnerável ao MS10-59 e com o executável abaixo conseguimos pegar a shell:
https://github.com/egre55/windows-kernel-exploits/tree/master/MS10-059%3A%20Chimichurri
![[Write-up/Hack The Box/Arctic/img/Pasted image 20240303152502.png]]