# cluster_base: Esse é a construção do cluster que eu utilizo para realizar meus processamentos

## 1. RECURSOS NECESSÁRIOS

1. INSTALAR O VIRTUAL BOX

2. BAIXAR UMA VERSÃO RECENTE DO CENTOS8-STREAM (<https://www.centos.org/download/>)

## 2. CONFIGURANDO A VIRTUALBOX
1. ABRA O VIRTUAL BOX E CLIQUE EM NOVO
2. DÊ UM NOME A SUA MÁQUINA VIRTUAL (no meu caso foi CLUSTER-CENTOS) E ESCOLHA LINUX PARA TIPO E REDHAT 64-BITS EM VERSÃO
3. CONFIGURE 4 GB DE MEMÓRIA RAN
4. CRIE UM NOVO DISCO RIGIDO VIRTUAL, E SELECIONE VDI(VIRTUALBOX DISK IMAGE)
5. COLOQUE DINAMICAMENTE ALOCADO
6. COLOQUE 80 GB DE ARMAZENAMENTO
7. CLIQUE EM CIMA DE SUA MÁQUINA VIRTUAL SELECIONANDO ELA, DEPOIS CLIQUE EM CONFIGURAÇÕES
8. EM CONFIGURAÇÕES SELECIONE SISTEMA-PROCESSADOR E COLOQUE 3 NUCLEOS
9. EM CONFIGURAÇÕES SELECIONE ARMAZENAMENTO, NA PARTE DE DISPOSITIVOS DE ARMAZENAMENTO CLIQUE EM CIMA DO DISCO DA PARTE CONTROLADORA: IDE
	9.1 NA PARTE DIREITA EM ATRIBUTOS - DRIVE OPTICO CLIQUE EM CIMA DO CD E SELECIONE ESCOLHER UMA IMAGEM DE DISCO, DEPOIS ESCOLHE A IMAGEM DO CENTOS
10. EM CONFIGURAÇÕES SELECIONE REDE, HABILITE PLACA DE REDE E COLOQUE CONECTADO A PLACA EM MODO BRIDGE 
11. NO MEU CASO, ATIVEI O DISPOSITIVO DE ACELERAÇÃO HYPER-M, MAS É OPCIONAL

## 3. INSTALAR O CENTOS8
1. INICIE A SUA MÁQUINA VIRTUAL
2. ESCOLHA A LINGUAGEM PORTUGUES > PORTUGUES DO BRASIL
3. EM SELEÇÃO DE PROGRAMAS ESCOLHA: INSTALAÇÃO MINIMA > PADRÃO + FERRAMENTAS DE DESENVOLVIMENTO 
3. EM DESTINO INSTALAÇÃO ESCOLHA O DISCO CRIADO NO PASSO 2
4. EM CONFIGURAÇÕES DE USUARIO ESCOLHA A SENHA DO ROOT
5. INICIE A INSTALAÇÃO DO SISTEMA
6. SE CONECT A SUA REDE DE INTERNET EM REDE E NOME DO HOST
7. CRIE UM USUARIO ADMINISTRADOR EM CRIAÇÃO DE USUARIO
8. INSTALE O PACOTE DE GERENCIAMENTO DE REDE NET-TOOLS
	```
	sudo yum install net-tools
	```
9. INSTALE O PYTHON3.9
	```
	sudo dnf groupinstall 'development tools'
	```
	```
	sudo dnf install bzip2-devel expat-devel gdbm-devel \
    	ncurses-devel openssl-devel readline-devel wget \
    	sqlite-devel tk-devel xz-devel zlib-devel libffi-devel
	```
	```
	wget https://www.python.org/ftp/python/3.9.0/Python-3.9.0.tgz
	```
	```
	tar -xf Python-$3.9.0.tgz
	```
	```
	cd Python-3.9.0
	./configure --enable-optimizations
	```
	```
	make -j 4
	```
	```
	sudo make altinstall
	```
10. TESTE O PYTHON 
	```
	python3.9.0 
	```

## 4. INSTALAR VIRTUAL ENV + PANDAS + JUPYTER
1. CRIE UMA PASTA PARA O PROJETO

```
mkdir "nome_da_pasta" 
```

2. ENTRE NA PASTA
	
```
cd "nome_da_pasta"
```
3. INSTALE O GERENCIADOR DE PACOTES DO PYTHON
	
```
sudo yum install python3-pip
sudo yum install python3-virtualenv
```
4. INSTALE O AMBIENTE VIRTUAL NA PASTA

```
virtualenv -p python3 "nome"
```
5. PARA ATIVAR A VIRTUAL ENV

```
source "nome"/bin/activate
```
6. INSTALE O PANDAS NO AMBIENTE VIRTUAL

```
pip install pandas
```

7. INSTALE O JUPYTER

```
pip install jupyter
```

8. PREPARE O JUPYTER 

```
ipython kernel install --name coleta_dados --user
```

9. SAIA DA SUA CONEXÃO SSH ATUAL E INICIE UMA NOVA

```
ssh -L 8000:localhost:8888 "nome_usuario"@"ip_maquina_virtual"
```

10. VA ATÉ A PASTA DE SEU AMBIENTE VIRTUAL E ENTRE NO SEU AMBIENTE VIRTUAL
11. INICIE SEU JUPYTER NOTEBOOK DENTRO DO SEU AMBIENTE VIRTUAL

```
jupyter notebook 
```
12. AGORA NO NAVEGADOR DO SEU COMPUTADOR HOST, COLOQUE A URL DO JUPYTER SUBISTINDO AS PORTAS 8888 POR 8000

## 5. INSTALAR MYSQL
1. ATUALIZE OS FLUXOS DO CENTOS	

```
sudo dnf upgrade --refresh -y
```
2. INSTALE A VERSÃO DE COMUNIDADE DO MYSQL

```
sudo dnf install mysql mysql-server -y
```
3. ATIVE O MYSQL

```
sudo systemctl enable mysqld --now
```
4. VERIFIQUE SE ELE ESTA RODANDO

```
sudo systemctl status mysqld
```
5. SE PRECISAR PARAR O MYSQL POR ALGUM MOTIVO

```
sudo systemctl stop mysqld
```
6. SE PRECISAR LIGAR DE NOVO

```
sudo systemctl start mysqld
```
7. DESABILITE A ATIVAÇÃO DO MYSQL AO LIGAR O SISTEMA

```
sudo systemctl disable mysqld
```
8. HABILITE A ATIVAÇÃO DO MYSQL AO LIGAR O SISTEMA

```
sudo systemctl enable mysqld
```
9. RESTARTAR O MYSQL

```
sudo systemctl restart mysqld
```

## 6. CONFIGURAR A CONEXÃO VIA PYTHON 
1. CONECTE A SUA MÁQUINA VIRTUAL 
```
ssh -L 8000:localhost:8888 root@192.168.1.109
```
2. ENTRE NO SEU CONTAINER PYTHON
```
source "caminho_ambiente_virtual/activate"
```
3. DENTRO DO AMBIENTE VIRTUAL, INSTALE O PACOTE MYSQL-CONNECTOR
```
pip install mysql-connector-python
```
## 7. INSTALAR MONGODB
1. CONFIGURE O REPOSITORIO 
```
sudo nano /etc/yum.repos.d/mongodb-org-6.0.repo
```
2. DENTRO DESSE ARQUIVO COLOQUE
```
[mongodb-org-6.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/6.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-6.0.asc
```
3. SALVE O ARQUIVO
4. INSTALE
```
sudo yum install -y mongodb-org
```
5. STARTE O MONGO
```
sudo systemctl start mongod
```
6. VERIFIQUE O STATUS
```
sudo systemctl status mongod
```
7. CONFIGURE PARA LIGAR AO INICIAR
```
sudo systemctl enable mongod
```
8. PARA USAR O MONGO
```
mongosh
```
## 8. CONFIGURAR A CONEXÃO VIA PYTHON-MONGODB
1. CONECTE A SUA MÁQUINA VIRTUAL 
```
ssh -L 8000:localhost:8888 antonio@"ip_maquina"
```
2. ENTRE NO SEU CONTAINER PYTHON
```
source "caminho_ambiente_virtual/activate"
```
3. DENTRO DO AMBIENTE VIRTUAL, INSTALE O PACOTE PYMONGO
```
pip install pymongo
```

## 9. Instalar docker 
1. Comandos:
```
 sudo yum remove docker docker-client  docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine
 sudo yum install -y yum-utils
 sudo yum-config-manager  --add-repo https://download.docker.com/linux/centos/docker-ce.repo
 sudo yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin
 sudo systemctl start docker
 sudo docker run hello-world
```

## 10. Criar container cloudera/docker
1. Comandos:
```
docker pull cloudera/quickstart:latest
sudo docker run --hostname=quickstart.cloudera --name=hadoop --privileged=true -t -i cloudera/quickstart /usr/bin/docker-quickstart
```
2. Após o passo 1, pode-se usar o seguinte comando para startar o container
```
sudo docker start hadoop
```
3. Para entrar, execute o comando:
```
sudo docker exec -it hadoop /usr/bin/docker-quickstart
```
4. Habilite o docker 
```
sudo systemctl enable docker
```
