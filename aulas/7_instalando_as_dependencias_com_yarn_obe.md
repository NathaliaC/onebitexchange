## Instalando as dependências

Vamos instalar o jQuery rodando:

Esse `yarn add jquery` basicamente vai procurar nos repositórios o jquery e vai colocar 
no `node modules`. Se você não conhece o yarn é igual ao npm. Então ele instala pacotes javascripts,
o legal o yarn é que ele faz um cache ali das coisas que ele instala, ele é um pouco mais rápido que 
o npm e ele olha nos repositórios do npm também. 
```sh
docker-compose run --rm app bundle exec yarn add jquery
```
E agora tudo que a gente precisa fazer é dizer para o nosso rails que queremos utilizar o jquey no projeto.  
Agora temos que importá-lo no nosso application.js, adicione a seguinte linha no seu arquivo:
```sh
//= require jquery
```
A mesma coisa que foi feita com o jquery será feita com o bootstrap. Só que o bootstrap tem dois lados, que é
a parte de `CSS` que é a parte que da estilo aos botões e as viewa e a parte de `javascript`que controla
esses comportamentos mais dinâmicos como modal, dropdown e etc.
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
