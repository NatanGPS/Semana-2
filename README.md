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

        sudo docker run -e POSTRES_PASSWORD=123 --name ContainerPostgresNome -v PostgresVolume:/var/lib/postgresql/data -d ppostgres:latest

<br> Pronto agora podemos seguir o mesmo passo a passo anterior para criar uma tabela e inserir coisas nela, e mesmo que por algum motivos nosso container seja apagado todos os dados ficarão salvos no nosso bolume denominado de PostgresVolume.
