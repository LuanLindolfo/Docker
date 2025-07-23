# Docker na AWS
O presente arquivo demonstra o exercício básico e prático utilizando conteinerização e o serviço de computação em nuvem, utilizando o sistema operacional windows como origem e o ubuntu na computação em nuvem como destino.

## Tecnologias Utilizadas
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat&logo=docker&logoColor=white)
![Docker Hub](https://img.shields.io/badge/Docker_Hub-000000?style=for-the-badge&logo=docker&logoColor=white)
![Docker Compose](https://img.shields.io/badge/Docker_Compose-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-232F3E?style=for-the-badge&logo=amazon-aws&logoColor=white)
![YAML](https://img.shields.io/badge/YAML-CB171E?style=for-the-badge&logo=yaml&logoColor=white)
![Rancher](https://img.shields.io/badge/Rancher-0075A8?style=for-the-badge&logo=rancher&logoColor=white)
![Windows](https://img.shields.io/badge/Windows-0078D4?style=for-the-badge&logo=windows&logoColor=white)
![Ubuntu](https://img.shields.io/badge/Ubuntu-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)

## Imagem do Container
Começando com os fundamentos bases do serviço de conteinerização, foi criada uma imagem feita com fundamentos chaves que proporcionam maior abrangência do serviço que vai ser proposto, feito em linguagem YAML que serve para Data Serialization, ou seja, para escrever códigos e arquivos de configurações sendo lidos de cima para baixo com indentação.

### Código com extensão .yml
```
#versão - pode ser qualquer uma
version: "3.8"

#importante explicar quem depende de quem -> depends_on:
#ser no início pra ler a dependência antes de executar o build

#serviços - nome dos containers
services:
  frontend:
  #informando a dependência
    depends_on:
      - backend
  #encaminha ao diretorio
  #constrói conforme os arquivos do diretório frontend
    build: ./frontend
  #portas do front
    ports:
    #de 3000 para 3000
      - 3000:3000

  backend:
    depends_on:
      - db
  #encaminha ao diretorio
  #constrói conforme os arquivos do diretório backend
    build: ./backend
  #portas do back
    ports:
    #de 3001 para 3001
      - 3001:3001
    #backend tem uma conexão como banco de dados
    environment:
      DB_URL: mongodb://db/vidly
    command: ./docker-entrypoint.sh

  db:
  #define a image,
    image: mongo:4.0-xenial
  #portas do db -> basta pesquisar a porta do mongodb no google
    ports:
    #de 27017 para 27017
      - 27017:27017
    volumes:
    #volume do db
    #vai armazenar dentro de /data/db
      - vidly:/data/db

#volumes
volumes:
  vidly:
```
O código inclui estruturas frontend, backend e de Banco de Dados que tem relação de dependência. Os demais arquivos da imagem e do container, bem como o auxílio de construção de código, foram fornecidos pelo Professor Andre Iacono ministrando o curso [Aprenda Docker do Zero: Construa, Teste e Implemente Containers com Aulas 100% Práticas](https://www.udemy.com/course/docker-zero-avancado/) na plataforma de cursos Udemy. É importante lembrar que o Docker Compose foi utilizado para que sjea criado um container para cada dependência da aplicação, ele já vem instalado por padrão e é possível verificar pelo comando
```Shell
docker compose version
```
Caso não esteja instalado, basta instalar conforme o passo a passo informaod pelo Digital Ocean - [Como instalar e utilizar o Docker Compose no Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-compose-on-ubuntu-20-04-pt)

### Build da imagem
Ao entrar no diretório do arquivo da aplicação, o passo a ser tomado é a build da imagem de forma interativa sem perder a linha de comando e nomeando a imagem e a tag, dessa forma foi utilizado o comando:
```Shell
docker build -t app:v1.0.0 .
```
Em seguida, basta entrar na imagem para verificar o funcionamento. É possível entrar na imagem, de forma interativa, com o comando
```
docker run -it app sh
```
Após atestar o funcionamento, basta digitar "exit" ou inserir o comando 'Ctrl + C' para sair da imagem. Em seguida, confirme a criação da imagem e a saída da execução pelo Rancher Desktop > Images. Sua imagem vai constar com a tag adicionada.

### Upload no DockerHUB
Em seguida, utilize o [DockerHUB](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwjd9YK8ldOOAxWJvJUCHRPgMO4QFnoECCYQAQ&url=https%3A%2F%2Fhub.docker.com%2F&usg=AOvVaw08oOGeIqomrCmPs9p07hDk&opi=89978449) e crie uma conta.

Em seguida, crie um repositório, de preferência com o nome da imagem, para que possamos inserir a imagem de modo que fique armazenada.

Após a criação,insira o comando para ver as imagens e as informações delas
```Shell
docker images
```
Copie o caminho do diretório 'caminho/imagem' e entao renomeie na imagem com a tag do repositório
```Shell
docker image tag (ID) luanmello12/app:v1.0.0
```

Nesse caso, a imagem se chama "app" com a tag de versão v1.0.0 e o repositório no dockerhub tem o caminho "luanmello12/app", a ID foi colhida do comando de informação de imagens.

Utilizando o comando de login no dockerhub, é possível conectar à plataforma, sendo possível logar com um código gerado para inserir no site.

```Shell
docker login
```

Depois, basta inserir a imagem com o comando "push" e as informações da imagem
```Shell
docker push luanmello12/app:v1.0.0
```
Para confirmar o sucesso no upload, abra o repositório no DockerHUB e verifique a importação da imagem com a tag.

### Utilização na AWS
#### Docker na AWS - Instalação Fonte
No serviço em nuvem, a máquina vem sem nenhum serviço ligado ao docker, sendo necessária a instalação que é ensinada pelo Digital Ocean-[Como Instalar e Utilizar o Docker no Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-20-04-pt)

#### Passo a passo
Criando uma EC2 com o sistema operacional Ubuntu, com configurações personalizadas de VPC, sub-rede e grupo de segurança; basta entrar na máquina e inserir o comando de uso de usuário root
```Shell
sudo su
```
Fazendo novamente o login com o comando
```Shell
docker login
```
Utilize o comando de inserção da imagem no container, no host em questão, comando esse que o próprio DockerHUB fornece ao abrir o diretório dos arquivos bastando apenas informar a TAG da imagem.
Nesse caso, mantendo o tutorial, o repositório Wluanmello/repteste" seguido da TAG de versão v1.0.0
```Shell
docker push luanmello12/repteste:v1.0.0
```
Será feito o download da imagem e em seguida basta utilizar o comando run para rodar a imagem
```Shell
docker run -it luanmello12/repteste:v1.0.0 sh
```
Será rodada a imagem de forma interativa no shell sem perder a linha de comando.

### Extra
Também é possível acessar o diretório sem entrar no container 
