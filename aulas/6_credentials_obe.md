## Credentials

No rails 5.2 foi criado uma nova forma de você guardar seus dados sensíveis, chamados Credentials. 

Basicamente você tem dois arquivos dentro do seu projeto pra fazer isso. Que são o credentials.yml.enc,
que é um arquivo criptografado e todas as chaves que você tiver, serão colocadas nesse arquivo quando 
a gente abrir ele, ou seja, você vai colocar a chave para essa API, o token pra serviços da AWS, qualquer
token que você precise e depois de fechar esse arquivo o rails vai criptografar ele e vai ser impossível de
ler sem o arquivo master.key. 

Dentro do `master.key` existe um hash secreto também que é gerado quando você gera o projeto e ele serve
para discriptografar o credentials.yml.enc. 

1 - Vamos logar na máquina docker para abrir nosso credentials
```sh
docker-compose run --rm app bash 
```

Aqui a gente ta dizendo que o editor de texto que a gente vai usar pra editar o arquivo é o nano e
vamos rodar `bundle exec` que é simplesmente pra ele pegar o contexto atual das gems e depois rails credentials:edit
2 - Abra o editor
```sh
EDITOR=nano bundle exec rails credentials:edit 
```

3 - No editor, insira as linhas:
```sh
EDITOR=nano bundle exec rails credentials:edit 
```

Devemos separar as credenciais por ambientes para evitar custos. 
```sh
test:
  secret_key_base: c6658efe5424dd97a9e651608915f7fd6205e4e8c8411c93657c9896279cac6625cf23cd3989c30cfebeab595f97d4deaf70e25d888bd7b71af41702e9a36ae6
  currency_api_key: sua_api_key
  currency_api_url: https://currencydatafeed.com/api/data.php
 
 
development:
  secret_key_base: c6658efe5424dd97a9e651608915f7fd6205e4e8c8411c93657c9896279cac6625cf23cd3989c30cfebeab595f97d4deaf70e25d888bd7b71af41702e9a36ae6
  currency_api_key: sua_api_key
  currency_api_url: https://currencydatafeed.com/api/data.php
 
 
production:
  secret_key_base: c6658efe5424dd97a9e651608915f7fd6205e4e8c8411c93657c9896279cac6625cf23cd3989c30cfebeab595f97d4deaf70e25d888bd7b71af41702e9a36ae6
  currency_api_key: sua_api_key
  currency_api_url: https://currencydatafeed.com/api/data.php
```

#### Explicando cada ponto:

Então a gente vai ter o `currency_api_key`, que é a chave, aquele token que a gente criou antes. 
Então a gente vai ter o `currency_api_url`, que é a url, que a url do nosso serviço de conversão de moedas. 

Então essa é a forma mais segura de salvar suas credenciais no rails 5.2. 

