Enumeração dos erviços:
![[Pasted image 20240129144922.png]]

Listagem das pastas compartilhadas:
![[Pasted image 20240129100509.png]]

Conexão com pasta remota e extração do arquivo passwords.txt:
![[Pasted image 20240129100721.png]]

Decodificação das senhas:
![[Pasted image 20240129100813.png]]

```
Bob:!P@$$W0rD!123
Bill:Juw4nnaM4n420696969!$$$
```

Validando as credenciais:
![[Pasted image 20240129101607.png]]

A pasta compartilhada via SMB é acessada via web na porta 49663. Subir a shell reversa em aspx.

Escalação de privilégio utilizando `whoami /priv` e o script PrintSpoof.exe.