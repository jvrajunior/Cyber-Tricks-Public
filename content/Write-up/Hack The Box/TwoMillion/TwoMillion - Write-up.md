Enumeração de serviços;
![[Write-up/Hack The Box/TwoMillion/img/Pasted image 20240222081958.png]]
Inclusão do domínio 2million.htb no hosts
Descoberta do endpoint /invite e analise do código fonte.
![[Write-up/Hack The Box/TwoMillion/img/Pasted image 20240223085117.png]]Descoberta de função Javascript que pode ser executada através do console, retornando uma mensagem criptografada em ROT13.
![[Write-up/Hack The Box/TwoMillion/img/Pasted image 20240223085304.png]]
![[Write-up/Hack The Box/TwoMillion/img/Pasted image 20240223085613.png]]

A decodificação da mensagem leva ao endpoint de criação do código para cadastro no site.
![[Write-up/Hack The Box/TwoMillion/img/Pasted image 20240223085812.png]]
![[Write-up/Hack The Box/TwoMillion/img/Pasted image 20240223090505.png]]
Cadastro e acesso ao site com código gerado:
![[Write-up/Hack The Box/TwoMillion/img/Pasted image 20240223090604.png]]
Temos algumas opções dentro da área logada do site, mas nada muito interessante. Vamos então validar o endpoint /api a fim de descobrir outros endpoints da aplicação.
![[Write-up/Hack The Box/TwoMillion/img/Pasted image 20240225105243.png]]
Dessa forma descobrimos alguns endpoints administrativos, sendo o mais interessante o de atualização das configurações do usuário. Vamos verificar as configurações do usuário atual:
Request /api/v1/user/auth
Com os dados retornados, vamos tentar atualizar o parâmetro is_admin com a seguinte request:
![[Write-up/Hack The Box/TwoMillion/img/Pasted image 20240225105519.png]]
Agora nosso usuário é administrador, vamos navegar mais um pouco no site.
Nenhum nova opção foi liberada no site, voltamos então a API e durante a geração de uma nova VPN para um usuário o sistema pode usar funções como exec ou System se tratano de PHP, vamos então tentar uma injeção de comando com `;id;` após o nome de usuário:
![[Write-up/Hack The Box/TwoMillion/img/Pasted image 20240225110631.png]]
Temos com Command Injection, agora podemos pegar uma shell reversa.
![[Write-up/Hack The Box/TwoMillion/img/Pasted image 20240225110919.png]]
Dados do banco de dados encontrados no arquivo .env:
![[Write-up/Hack The Box/TwoMillion/img/Pasted image 20240225111046.png]]
Ao fazer a enumeração de arquivos do usuário `admin` encontramos um e-mail indicando que existem diversas CVEs para o kernel atual do servidor web.
![[Write-up/Hack The Box/TwoMillion/img/Pasted image 20240225111714.png]]
Ao tentar conextar via SSH com os dados encontrados no arquivo .env, conseguimos fazer a movimentação lateral:
![[Write-up/Hack The Box/TwoMillion/img/Pasted image 20240225114235.png]]
Após algumas pesquisas encontramos o exploit para a [CVE-2023-0386](https://github.com/xkaneiki/CVE-2023-0386) indicado no e-mail que encontramos.
Fazemos o git clone, compactamos e enviamos para o alvo.
Após fazer o upload, vamos descompactar no alvo e rodar os comando para executar o exploit.
``` bash
make all
./fuse ./ovlcap/lower ./gc &
./exp
```
![[Write-up/Hack The Box/TwoMillion/img/Pasted image 20240225115121.png]]