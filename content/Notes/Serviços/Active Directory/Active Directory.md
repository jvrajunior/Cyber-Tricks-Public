AD (Active Directory) é utilizado por administradores de rede para criarem e gerenciarem de forma eficiente domínios, usuários e objetos dentro de uma rede.

Analise de vulnerabilidades e identificação de potenciais ataques dentro de uma rede:
1. Faça a instalação do banco de dados usado pelo BloodHound:
```bash
sudo apt install -y neo4j
``` 
2. Inicie o Neo4j com o comando
```bash
sudo neo4j start
```
3. Faça o download do BloodHound, extraia e execute o programa.
```bash
# Repositório no Github: https://github.com/BloodHoundAD/BloodHound/releases
wget https://github.com/BloodHoundAD/BloodHound/releases/download/v4.3.1/BloodHound-linux-x64.zip
unzip BloodHound-linux-x64.zip
./BloodHound-linux-x64/BloodHound
``` 
4. Após iniciar o BloodHound, acesse http://localhost:7474 e utilize o login `neo4j:neo4j` para configuração da senha definitiva.
5. Agora devemos baixar o binário que será executado na máquina alvo, o mesmo se encontra no repositório do BloodHound, dentro de `Collectors/SharpHound.exe`.
```bash
git clone https://github.com/BloodHoundAD/BloodHound
```
6. Faça o upload do SharpHound.exe para o alvo e execute.
7. Após a execução, será gerado um arquivo zipado com o resultado do escaneamento. Faça o download para a máquina atacante.
8. Faça o upload do arquivo baixado no painel do BloodHound para analise do arquivo.
9. Com o arquivo carregado, procure pelo usuário que já foi comprometido e com o botão direito marque a opção "Mark User as Owned".
10. Avalie todas informações coletadas e verifique os exploits indicados.