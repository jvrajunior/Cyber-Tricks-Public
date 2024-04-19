Enumeração de serviços:
![[Write-up/Hack The Box/Crafty/img/Pasted image 20240410094545.png]]

Enumeração de diretórios:
![[Write-up/Hack The Box/Crafty/img/Pasted image 20240410094602.png]]

Configuração do primeiro exploit:
```bash
git clone https://github.com/kozmer/log4j-shell-poc
```

Configurando o jdk:
```bash
#Copy in the '/log4j-shell-poc' directory  
wget https://repo.huaweicloud.com/java/jdk/8u181-b13/jdk-8u181-linux-x64.tar.gz  
  
tar -xf jdk-8u181-linux-x64.tar.gz #To extract  
  
mv jdk1.8.0_181 jdk1.8.0_20 #poc.py is searching for this filename
```

Configuração do segundo exploit:
```bash
git clone https://github.com/ammaraskar/pyCraft
```

Configurando o ambiente virtual:
```bash
virtualenv ENV  
  
source ENV/bin/activate  
  
pip install -r requirements.txt
```

![[Write-up/Hack The Box/Crafty/img/Pasted image 20240410150724.png]]

Descoberta de arquivo .jar:
![[Write-up/Hack The Box/Crafty/img/Pasted image 20240410153036.png]]

Debug do arquivo jar:
![[Write-up/Hack The Box/Crafty/img/Pasted image 20240410153429.png]]

Shell reversa utilizando as credenciais do administrador:
![[Write-up/Hack The Box/Crafty/img/Pasted image 20240410154314.png]]