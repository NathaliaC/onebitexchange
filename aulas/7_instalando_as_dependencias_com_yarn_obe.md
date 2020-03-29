## Instalando as dependências

Vamos instalar o jQuery rodando:
```sh
docker-compose run --rm app bundle exec yarn add jquery
```

Agora temos que importá-lo no nosso application.js, adicione a seguinte linha no seu arquivo:
```sh
//= require jquery
```

Agora vamos instalar o bootstrap no nosso projeto:
```sh
docker-compose run --rm app bundle exec yarn add bootstrap
```

Agora renomeie o arquivo app/assets/application.css para application.scss e substitua o conteúdo dele por:
```sh
@import "bootstrap/dist/css/bootstrap.css";
```

No application.js coloque:
```sh
//= require bootstrap/dist/js/bootstrap
```
