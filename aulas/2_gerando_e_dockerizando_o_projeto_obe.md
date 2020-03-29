# Gerando e Dockerizando o Projeto 
### Vamos criar nosso projeto, dentro da pasta desejada, rode no terminal os comandos abaixo: 

```sh

docker run --rm --user "$(id -u):$(id -g)" -v $(pwd):/usr/src -w /usr/src -ti ruby:2.5.1 bash
```

Detalhes sobre o comando: 
- `docker run` = Rode claro
- Máquina `ruby:2.5.1`
- `--user "$(id -u):$(id -g)"` Conectando o meu usuário com o usuário da máquina docker
- `-v $(pwd):/usr/src` Aqui eu to conetando a minha pasta atual dentro do meu terminal com a pasta dentro do docker. Veja bem, um truque interessante é que no docker geralmente tem esses dois pontos, do lado esquerdo é sempre o que é referente a tua máquina e do lado direito é o que referente a máquina dentro do docker. 
- `-w` Aqui o que eu to falando que quando o workdi, quando eu entrar nessa máquina ou rodar um comando nessa máquina docker, ele sempre vai executar a partir dessa pasta `/usr/src`. 
- `bash` Quando eu executo o docker e escolho a imagem, eu também posso passar um comando pra ela, nesse caso foi escolhido o bash. 

Instalar o rails dentro da máquina
```sh
gem install rails -v 5.2
```
Comando para criar o projeto. Como você bem sabe o rails é um conjunto de arquivos e pastas, ele é um framework. Então para gerar esses arquivos e pastas, esse é o generate. Nesse caso já estamos gerando o projeto com a gem do banco de dados e com o comando `--skip-bundle`você pode falar pra ele que você não quer que ele gere automaticamente o `bundle install`, comando que instala as depedências do projeto. 
```sh
rails new OneBitExchange --database=postgresql --skip-bundle
```

```sh
exit

cd OneBitExchange
```

Agora vamos dockerizar o projeto. Na raiz do projeto crie um arquivo chamado Dockrfile e insira o conteúdo:

```docker
FROM ruby:2.5.1
 
# add nodejs and yarn dependencies for the frontend
RUN curl -sL https://deb.nodesource.com/setup_6.x | bash - && \
    curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - &&\
    echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list
 
# Instala nossas dependencias
RUN apt-get update && apt-get install -qq -y --no-install-recommends \
    nodejs yarn build-essential libpq-dev imagemagick git-all nano
 
 
# Seta nosso path
ENV INSTALL_PATH /onebitexchange
 
# Cria nosso diretório
RUN mkdir -p $INSTALL_PATH
 
# Seta o nosso path como o diretório principal
WORKDIR $INSTALL_PATH
 
# Copia o nosso Gemfile para dentro do container
COPY Gemfile ./
 
# Seta o path para as Gems
ENV BUNDLE_PATH /gems

# Copia nosso código para dentro do container
COPY . .
```
###  Explicando os comandos:

#### O que é o Dockerfile? 
O dockerfile basicamente é um arquivo onde descreve a máquina onde o seu projeto vai ser executado, ele descreve as dependências que aquela máquina possui. Só quer, no docker você não cria uma máquina do zero, você sempre vai criar ela a partir de uma máquina pré-existente. No nosso caso, nos vamos pegar as máquinas pré-existentes no repositório do docker hub, chamado `ruyb:2.5.2` e ai a gente vai incluir algumas coisas nelas e ele vai guardar pra gente o resultado, a imagem dessa máquina pra sempre poder rodar o nosso projeto lá dentro. 

```docker
FROM ruby:2.5.1
```
Aqui no caso nós vamos instalar o node que é uma dependência ai do rails pra gente rodar a parte de javascript. Vamos instalar o yarn, ele que também é uma dependência para que a gente possa instalar o bootstrap ou materialize com facilidade e no último comando um pouo mais de configuração do yarn. 
```docker
# add nodejs and yarn dependencies for the frontend
RUN curl -sL https://deb.nodesource.com/setup_6.x | bash - && \
    curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - &&\
    echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list
```
Depois disso a gente vai dar um `update` na máquina, vai instalar os pacores recomendados, vamos instalar o node, adicionar o pacote do yarn, vamos instalar algumas libs que são dependências, vamos instalar o `imagemagick` que é um software que tu ajuda a editar imagem, instalar o git e o nano caso precisemos editar alguma arquivo ai dentro da nossa máquina no docker. Se você quiser instalar outros pacotes, você vai colocar aqui também. 
```docker
# Instala nossas dependencias
RUN apt-get update && apt-get install -qq -y --no-install-recommends \
    nodejs yarn build-essential libpq-dev imagemagick git-all nano
```

Aqui estamos instalar uma variável de ambiente chamada `INSTALL_PATH` ela vai armazenar o caminho para `/onebitexchange` tudo que rodar dentro da nossa máquina vai ser executado dentro dessa pasta. 
```docker
# Seta nosso path
ENV INSTALL_PATH /onebitexchange
```

Aqui nos vamos criar ela dentro do diretório setado na nossa variável de ambiente, ele vai criar essa pasta dentro da máquina. 
```docker
# Cria nosso diretório
RUN mkdir -p $INSTALL_PATH
```

Aqui nós estamos dizendo que o `WORDIR` é o `/onebitexchange`, todo comando que eu rodar ele roda direto nessa pasta ao invés de rodar no root da máquina. 
```docker
# Seta o nosso path como o diretório principal
WORKDIR $INSTALL_PATHRUN mkdir -p $INSTALL_PATH
```

Esse comando copiar o nosso arquivo Gemfile para dentro da máquina docker quando você executa o `docker build` pra depois poder instalar essas gems. 
```docker
# Copia o nosso Gemfile para dentro do container
COPY Gemfile ./
```

Depois nós vamos criar uma varíavel com o nome `BUNDLE_PATH` com o caminho `/gems`, o que acontece? A gente quer criar um volume especial só pra guardar as gems depois que elas forem instaladas e o `BUNDLE PATH` ele procura e falar pro rails procurar lá as gems. Se não a gente precisaria toda vez que matasse a máquina ficar reinstalando as gems e tudo mais e iria ser bem penoso pra gente e tendo um volume separado com as gems fica bem mais fácil. 
```docker
# Seta o path para as Gems
ENV BUNDLE_PATH /gems
```

E aqui a gente tem um `copy` geral, onde nós vamos copiar tudo que tem na nossa máquina lá pra dentro. 
```docker
# Copia nosso código para dentro do container
COPY . .
```

E continuando aqui...Depois que nós criarmos o nosso `Dockerfile`, nós iremos criar o nosso `docker-compose`. 

### Dokcer Compose
É o cara que vai gerenciar todos os serviços juntos, ou seja, ele é o cara que vai subir essa máquina que a gente acabou de criar, vai subir um postgresql, vai subir um redis da vida, vai subir todos os caras que serão necessários pra gente

Agora crie também na raiz do projeto o arquivo docker-compose.yml e insira nele:
```Docker
version: "3"
 
services:
  postgres:
    image: "postgres:9.5"
    volumes:
      - postgres:/var/lib/postgresql/data
    environment:
      POSTGRES_HOST_AUTH_METHOD: "trust"
 
  app:
    depends_on:
      - "postgres"
    build: .
    command: bash start.sh
    ports:
      - "3000:3000"
    volumes:
      - .:/onebitexchange
      - gems:/gems
 
 
volumes:
  postgres:
  gems:
```


### Explicando cada ponto do arquivo:

O docker-compose tem várias sintáxe, no nosso caso estamos na `versão 3`. 
```Docker
version: "3"
```

No nosso caso a gente vai ter dois services, services são máquinas, poderíamos abstrair essa ideia como máquinas. A gente vai ter máquina `postgres` que é onde roda o nosso banco de dados e vai ter a nossa máquina `rails`, que é a máquina relacionada ao Dockerfile. 
```Docker
services:
  postgres:
    image: "postgres:9.5"
```

No nosso caso vamos usar o `postgres 9.5`, estou falando que quero que ele pegue a `imagem 9.5 do postgres` la no docker hub e o `volume` que é a pasta onde isso vai ficar guardado é o `postgres:/var/lib/postgresql/data`.
```Docker
    volumes:
      - postgres:/var/lib/postgresql/data
```

E aqui a gente vai ter o `app`, o app é basicamente a nossa máquina rails e ele primeiro depende do postgres e a gente configura isso com o `depends_on:`, ou seja, ele não vai subir se o postgres não funcionou. 
```Docker
  app:
    depends_on:
      - "postgres"
```

Ao invés dele ter um imagem, ele tem um `build .`, build . quer dizer que ao ínves dele usar uma imagem, olhe se existe um `Dockerfile` no projeto e use ele como imagem. Execute ele e use ele como imagem. 
```Docker
    build: .
```

`command: bash start.sh` siginifica que quando essa máquina terminar de subir, ela vai rodar esse command. E esse comand ta relacionado com um arquivo script que a gente vai criar já já, que é responsável por ver se as gems estão instaladas e instalar e se elas estiverem instaladas subir esse projeto via puma. 
```Docker
    command: bash start.sh
```

`ports` Ele vai mapear a porta interna 3000:3000 para a porta interna da nossa máquina 3000. 
```Docker
    ports:
      - "3000:3000"
```

`volumes` Volumes são essas pastas que a gente conecta. Onde `.:/` à esquerda é a pasta do nosso direótório atual e o `/onebitexchange` é a pasta atual dentro da máquina. Do lado esquerdo `gems` e do lado direito também. 
```Docker
    volumes:
      - .:/onebitexchange
      - gems:/gems
```

E os volumes são essas pastas que a gente conecta. 
```Docker
volumes:
  postgres:
  gems:
```

## Start SH
Vamos criar agora o arquivo `start.sh`
```sh
# Instala as Gems
bundle check || bundle install
 
# Roda nosso servidor
bundle exec puma -C config/puma.rb
```

O `bundle check` verifica se todas as gems do Gemfile estão instaladas, se não, ele executa o `bundle install`.

E ai se der tudo certo ele executa o `bundle exec puma -C config/puma.rb`, basicamente o que ele quer dizeer é, suba o servidor puma, que é o servidor do rails de desenvolvimento, usando o arquivo de configuração `config/puma.rb`. 

Esse arquivo já existe, ele vem com o próprio rails. Que é uma maneira mais explícita de fazer o `rails s`. 

### Executando 
Vamos agora fazer o build do projeto para criar nosso ambiente rode esse comando deve baixar todas as dependências descritas no arquivo `Dockerfile`. 
```sh
docker-compose build 
```

Caso seus comandos docker só funcionem usando o sudo antes, rode no console os seguintes comando para concertar:
```sh
sudo groupadd docker
sudo usermod -aG docker $USER
sudo service docker restart
```