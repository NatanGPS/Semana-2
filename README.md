# Semana-2
## Desafio 1: Baixar e instalar Docker

## Desafio 2: Criar uma imagem do postgreSQL rodar a imagem e persistir dados do banco em um volume:
### Primeiro etapa: Precisamos primeiramente criar nossa imagem do banco de dados, para isso podemos utilizar diversos comandos mas aqui vou o utlizar o pull:

    sudo su
    docker pull postgres sql
<br>Podemos conferir se instalação deu certo executando o comando

    docker images
<br> É importante entendermos para que usaremos volumes para isso vamos rodar um container com nosso postgre utilizando o comando docker run sem a utilização de um volume para entendermos melhor a importancia dele. Então rodamos nossa imagem em um container dessa forma abaixo: 

  
    docker run -e POSTGRES_PASSWORD=123 --name postgresSemvolume -d postgres:latest

<br> rodamos nosso container e definimos a senha do nosso usuario postgres como sendo 123. Podemos verificar se nosso container está rodando digitando o comando
    
    docker ps
<br> Agora podemos entrar no bash do no nosso container com o comando:

    docker exec -it postgresSemVolume bash
<br> Já estamos dentro do nosso container, vamos então conectar no nosso banco para podermos criar uma tabela e inserir dados na mesma atentesse aos atributos que iremos passar no comando, (-h) significa nosso host no caso localhost, (-U) nosso usuario como nenhum foi definido podemos utilizar o base 'postgres' e por final (-W) a senha que definimos quando rodamos nosso container

    psql -h localhost -U postgres -W
<br> Após entrar com o comando o console irá pedir sua senha após digitar voce já estará dentro do postgre, podemos conferir se temos alguma tabela criada usando o comando:
\l e veremos que existe o database padrao postgres, posteriormente podemos usar \dt e conferir que tambem não existe nenhuma tabela no momento
<br>Certo, então vamos criar uma nova tabela que irei chamar de pessoa, além disso criarei duas colunas sendo a primeira o ID, e o nome: 

    create table pessoa(id INTEGRER PRIMARY KEY, nome VARCHAR);
<br> Nossa tabela foi criada, podemos ve-la digitando \dt novamente, agora podemos inserir dados nessa tabela da seguinte forma:

    
    insert into pessoa values(1, 'natan');
<br> Pronto, mas agora podemos nos perguntar, em uma situação real onde eu tenha um banco dentro de um container, uma vez que preciso atualizar a versão desse banco ou por algum motivo eu precise reinstalar o que acontece com meus dados? Por esse motivo criei esse exemplo, em uma situação de estudo como essa, um simples banco criado dessa forma pode dar conta de tudo, mas quando pensamos na produção precisamos sempre de uma valvula de escape caso algo de errado em nosso projeto, esse é um dos motivos para existencia dos volumes em nossos containers ele pode trazer mais confiabilidade para nosso container e permitir que possamos atualizar e fazer outras coisas com nosso container sem comprometer nossos dados importantes.

<br> Certo, mas como criamos um banco com volume? Para isso podemos utilizar o parametro -v no nosso comando docker container run, vamos ver na pratica como fica:

        sudo docker run -e POSTRES_PASSWORD=123 --name ContainerPostgresNome -v PostgresVolume:/var/lib/postgresql/data -d postgres:latest

<br> Pronto agora podemos seguir o mesmo passo a passo anterior para criar uma tabela e inserir coisas nela, e mesmo que por algum motivos nosso container seja apagado todos os dados ficarão salvos no nosso volume denominado de PostgresVolume.

## Desafio 3: Criando container wordpress e mysql com docker compose 
### Instalando docker compose: 
<br> Primeiramente precisamos instalar o docker compose, para isso vamos utilizar a documentação do docker compose para isso podemos utilizar a instalação usando o repositorio deles no github, vamos começar, no terminal digite:

        sudo yum update
        sudo yum install docker-compose-plugin
<br> Vamos conferir se tudo correu certo digitando: 

        sudo docker compose version

<br> Pronto! Nosso docker compose está devidamente instalado

<br> Agora para configurar nossos containers que vamos subir usando o compose precisamos criar um diretorio com nome e local de nossa preferencia:

        sudo mkdir wordpress
<br> Vamos entrar em nosso diretorio e criar um arquivo chamado docker-compose.yml, para isso podemos utilizar a funação touch

        cd wordpress
        sudo touch docker-compose.yml
<br> Agora abrimos o arquivo utilizando o editor de preferencia

        sudo nano docker-compose.yl
<br> Com nosso arquivo aberto podemos escrever nossas configurações de container da seguinte forma:

        services:
      db:
        # We use a mariadb image which supports both amd64 & arm64 architecture
        image: mariadb:10.6.4-focal
        # If you really want to use MySQL, uncomment the following line
        #image: mysql:8.0.27
        command: '--default-authentication-plugin=mysql_native_password'
        volumes:
          - db_data:/var/lib/mysql
        restart: always
        environment:
          - MYSQL_ROOT_PASSWORD=somewordpress
          - MYSQL_DATABASE=wordpress
          - MYSQL_USER=wordpress
          - MYSQL_PASSWORD=wordpress
        expose:
          - 3306
          - 33060
      wordpress:
        image: wordpress:latest
        volumes:
          - wp_data:/var/www/html
        ports:
          - 80:80
        restart: always
        environment:
          - WORDPRESS_DB_HOST=db
          - WORDPRESS_DB_USER=wordpress
          - WORDPRESS_DB_PASSWORD=wordpress
          - WORDPRESS_DB_NAME=wordpress
    volumes:
      db_data:
      wp_data:
<br> Podemos agora salvar o arquivo com Ctrl + O e sair usando Ctrl + X.
<br> Agora para o proximo passo vamos nos certificar de estarmos no diretorio que criamos o arquivo, depois disso vamos digitar o seguinte comando para iniciar nossos containers

        sudo docker compose up -d

<br> Com o comando abaixo podemos verificar se os containers que criamos  estão ativos

        sudo docker container ls

<br> Se tudo correr bem voce pode simplesmente acessar em seu navegador sua pagina wordpress utilizando o seu_ip:porta, no meu caso fica assim

        193.168.2.108:80 
<br> Para verificar que nossos dados estão persistindo podemos desativar nossos containers e ativalo novamente para isso usamos os seguintes comandos em sequencia

        sudo docker compose down
        sudo docker compose up -d
<br> Se o wordpress ainda está configurado significa que os dados estão persistindo em nossos volumes

        
