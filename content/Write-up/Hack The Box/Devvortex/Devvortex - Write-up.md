Enumeração dos serviços:
![[Write-up/Hack The Box/Devvortex/img/Pasted image 20240405075419.png]]

Inclusão do domínio nos hosts:
```bash
sudo echo -e "10.10.11.242    devvortex.htb" >> /etc/hosts
```

Enumeração de diretórios:
![[Write-up/Hack The Box/Devvortex/img/Pasted image 20240405082358.png]]

Enumeração de subdomínios utilizando vhost:
```bash
gobuster vhost --append-domain -u devvortex.htb -w $wordlists/dns/subdomains-top1million-20000.txt -t 20
```
![[Write-up/Hack The Box/Devvortex/img/Pasted image 20240405083407.png]]

```bash
wfuzz -u http://devvortex.htb -H "Host: FUZZ.devvortex.htb" --hc 302 -w /usr/share/wordlists/seclists/Discovery/DNS/subdomains-top1million-20000.txt
```
![[Write-up/Hack The Box/Devvortex/img/Pasted image 20240405084708.png]]

Validação do robots.txt:
![[Write-up/Hack The Box/Devvortex/img/Pasted image 20240405085629.png]]

Página de login do joomla:
![[Write-up/Hack The Box/Devvortex/img/Pasted image 20240405090157.png]]

para descobrir a versão, vamos tentar acessar o arquivo: /administrator/manifests/files/joomla.xml
![[Write-up/Hack The Box/Devvortex/img/Pasted image 20240405090242.png]]

A versão possui uma vulnerabilidade conhecida de information disclosure, vamos então extrair essas informações através da API vulnerável:
https://www.exploit-db.com/exploits/51334

API de usuários:
```
http://dev.devvortex.htb/api/index.php/v1/users?public=true
```
![[Write-up/Hack The Box/Devvortex/img/Pasted image 20240405090948.png]]

API de configurações:
![[Write-up/Hack The Box/Devvortex/img/Pasted image 20240405091015.png]]

Com isso descobrimos um usuário e senha:
`lewis:P4ntherg0t1n5r3c0n##`

Com o login descoberto, obtemos acesso ao painel administrativo e verificamos que o usuário tem permissões de Super user.
![[Write-up/Hack The Box/Devvortex/img/Pasted image 20240405091559.png]]

Agora vamos editar a página de erro do template e inserir um codigo para execução de comando em PHP:
![[Write-up/Hack The Box/Devvortex/img/Pasted image 20240405091519.png]]
Ao acessar o arquivo modificado, temos um RCE:
![[Write-up/Hack The Box/Devvortex/img/Pasted image 20240405092400.png]]

Podemos então alterar novamente o arquivo e inserir uma shell reversa em PHP:
![[Write-up/Hack The Box/Devvortex/img/Pasted image 20240405093039.png]]

E conseguimos a shell:
![[Write-up/Hack The Box/Devvortex/img/Pasted image 20240405093203.png]]

Conexão ao banco de dados utilizando as mesmas credenciais: e extração do hash de outro usuário:
![[Write-up/Hack The Box/Devvortex/img/Pasted image 20240409223845.png]]

Quebrando o hash:
![[Write-up/Hack The Box/Devvortex/img/Pasted image 20240409231410.png]]

Identificação de permissão sudo:
![[Write-up/Hack The Box/Devvortex/img/Pasted image 20240409231451.png]]