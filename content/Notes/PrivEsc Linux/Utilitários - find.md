`find / -name $arquivo 2>/dev/null` Procura por um arquivo pelo nome.  
`find / -type d -name $diretorio 2>/dev/null` Procura por um diretório pelo nome.  
`find / -type f -perm 0777 2>/dev/null` Encontra arquivos com permissão total (Leitura, gravação e execução por todos usuários)  
`find / -perm a=x 2>/dev/null` Encontra arquivos executáveis.  
`find / -user $username 2>/dev/null` Encontra todos os arquivos de um usuário específico.  
`find / -mtime 10 2>/dev/null` Encontra arquivos modificados nos últimos 10 dias.  
`find / -atime 10 2>/dev/null` Encontra arquivos acessados nos últimos 10 dias.  
`find / -cmin -60 2>/dev/null` Encontra arquivos modificados na última hora.  
`find / -amin -60 2>/dev/null` Encontra arquivos acessados na última hora.  
`find / -size +50M 2>/dev/null` Encontre arquivos com 50 Mb de tamanho ou mais.  
`find / -writable -type d 2>/dev/null`  Encontrar diretórios com permissão de gravação.  
`find / -perm -222 -type d 2>/dev/null` Encontrar diretórios com permissão de gravação.  
`find / -perm -o w -type d 2>/dev/null` Encontrar diretórios com permissão de gravação.  
`find / -perm -o x -type d 2>/dev/null` Encontrar diretórios com permissão de execução.  
`find / -perm -u=s -type f 2>/dev/null` Encontrar arquivos com SUID.  