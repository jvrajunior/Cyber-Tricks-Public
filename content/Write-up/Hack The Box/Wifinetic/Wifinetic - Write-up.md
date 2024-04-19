Enumeração de serviços:
![[Write-up/Hack The Box/Wifinetic/img/Pasted image 20240225202058.png]]
Identificação do FTP habilitado login anônimo.
Navegando no arquivo MigrateOpenWrt.txt descobrimos que está ocorrendo uma migração do sistema Operacional OpenWRT pelo Debian.
![[Write-up/Hack The Box/Wifinetic/img/Pasted image 20240225202449.png]]
No arquivo employees_wellness.pdf identificamos informações importantes sobre um funcionário:
![[Write-up/Hack The Box/Wifinetic/img/Pasted image 20240225202705.png]]
ProjectOpenWRT.pdf:
![[Write-up/Hack The Box/Wifinetic/img/Pasted image 20240225202910.png]]
![[Write-up/Hack The Box/Wifinetic/img/Pasted image 20240225203042.png]]
Buscando senhas no arquivo extraido:
![[Write-up/Hack The Box/Wifinetic/img/Pasted image 20240225204308.png]]
```bash
VeRyUniUqWiFIPasswrd1!
```
Identificação de nome de usuário:
![[Write-up/Hack The Box/Wifinetic/img/Pasted image 20240225212346.png]]
Tentativa de login no SSH:
![[Write-up/Hack The Box/Wifinetic/img/Pasted image 20240225212627.png]]
Busca por capabilities e descoberto do binário reaver:
https://www.kali.org/tools/reaver/
![[Write-up/Hack The Box/Wifinetic/img/Pasted image 20240225224502.png]]
Descobrindo redes WiFi com ifconfig em busca do endereço do AP (Access Point):
![[Write-up/Hack The Box/Wifinetic/img/Pasted image 20240225224231.png]]
Identificação do AP através do `iw dev`
![[Write-up/Hack The Box/Wifinetic/img/Pasted image 20240225224828.png]]
Brute force para descoberta de senha do AP:
![[Write-up/Hack The Box/Wifinetic/img/Pasted image 20240225224914.png]]
Tentativa de troca para o usuário root com a senha encontrada:
![[Write-up/Hack The Box/Wifinetic/img/Pasted image 20240225224952.png]]
