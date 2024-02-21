`whoami /priv` Verifica os privilégios do usuário atual.  
`whoami /groups` Verifica os grupos do usuário atual.  
`Get-ADDomain` Verifica informações sobre o controle de domínio ao qual a máquina pertence.
`ver` Verificar a versão do Windows  
`wmic os get osarchitecture` Verificar a arquitetura do sistema operacional  
`systeminfo | findstr /B /C:"OS Name" /C:"OS Version" /C:"System Type"` Verifica nome e versão do Windows  
`route PRINT` Verificar as rotas de rede da máquina  
`netstat -ano` Verificar as conexões TCP e UDP  
`dir /R` Verificar arquivos com stream  
`powershell Get-Content -Path "hm.txt" -Stream "root.txt"` Extrair stream de arquivos  

## Ref:  
https://nored0x.github.io/red-teaming/windows-enumeration/  