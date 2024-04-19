Enumeração do serviços:
![[Write-up/Hack The Box/Busqueda/img/Pasted image 20240320193720.png]]

Softwares identificados:
```
Flask
Searchor 2.4.0
```

Exploração de CMD com exploit público:
https://github.com/nikn0laty/Exploit-for-Searchor-2.4.0-Arbitrary-CMD-Injection/blob/main/exploit.sh
![[Write-up/Hack The Box/Busqueda/img/Pasted image 20240320200557.png]]

Navegando entre os arquivos do alvo, foi possível descobrir o que parece ser uma credencial dentro de config em .git:
![[Write-up/Hack The Box/Busqueda/img/Pasted image 20240320213931.png]]
`cody:jh1usoih2bkjaspwe92`

Em uma tentativa de conectar via SSH com o usuário em questão não funcionou, mas ao utilizar a mesma senha com o usuário svc conseguimos conectar via SSH.
![[Write-up/Hack The Box/Busqueda/img/Pasted image 20240320214306.png]]

Ao verificar as permissões de SUDO do usuário, identificamos que ele excuta um script com python3 como root:
![[Write-up/Hack The Box/Busqueda/img/Pasted image 20240320214257.png]]

