## Setup inicial

Adicione no seu Gemfile dentro do bloco 'group:development', :test

```sh
gem 'rspec-rails', '~> 3.5'
```

#### Instale as gems

Esse comando fala para máquina que ele deve executar dentro da máquina docker de nome `app` o comando `bundle install` e o `--rm` significa que o container gerado para executar esse comando deve ser destruído ao finalizar a tarefa solcitada.
```sh
docker-compose run --rm app bundle install
```

#### Instale o Rspec

Gera e instala os arquivos de configurações principais do Rspec: 
```sh
docker-compose run --rm app bundle exec rails generate rspec:install
```
Precisamos do `bundle exec` por causa das questões das gems, as gems não estão no path natural e a gente ta setando elas em outros lugares, então o bundle exec esta pegando o contexto certinho do app.

No Log vemos que foi criado uma pasta rspec e dentro dela nós temos os arquivos `spec_helper.rb`e `rails_helper.rb`

Essa ferramenta de teste tem alguns arquivos de configuração que foi o que eu citei anteriormente.

Continuando a gente tem a configuração do banco de dados ...

## Banco de Dados

Altere seu arquivo config/database.yml para:
```yml
default: &default
  adapter: postgresql
  encoding: unicode
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  host: postgres
  user: postgres
 
 
development:
  <<: *default
  database: V2OneBitExchange_development
 
 
test:
  <<: *default
  database: V2OneBitExchange_test
 
 
production:
  <<: *default
  database: V2OneBitExchange_production
  username: OneBitExchange
  password: <%= ENV['EXCHANGE_DATABASE_PASSWORD'] %>
```

Aqui eu digo onde está esse banco postgre, a localização, url desse banco. 
```yml
  host: postgres
```

Vamos criar o nosso banco de dados:
```sh
    docker-compose run --rm app bundle exec rails db:create db:migrate
```

Subir a aplicação:
```sh
    docker-compose up
```