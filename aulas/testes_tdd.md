## Testes e TDD

#### O que são testes? 
Testes no mundo da programação são scripts que simular o uso
de algum recurso, como controllers, models, mailers, comportamento
e etc para ver se eles estão funcionando como deveriam. 

#### Porque eles são importantes?
A nossa visão é limitada a um ponto por vez, mas os sistemas que
criamos tem milhares de pontos, então quando você tem uma cobertura
completa de testes você garante que a sua mente não te engane e te
leve ao erro.

#### MineTest Vs Rspec
O Raisl vem com uma biblioteca padrão de teste chamada `MiniTest`, mas existe uma 
outra biblioteca chamada `Rspec`. 

O Rspec é a biblioteca mais utilizada e a preferida dos desenvolvedores. A Minites é 
a padrão. 


#### O que é o TDD?
Tdd é o desenvolvimento de software orientado a testes, ou seja, a criação des testes vem antes 
da criação do software. 

#### O Ciclo do TDD (TESTE DRIVER DEVELOPMENT) 
1 - Escrever testes que vão falhar. FALHA 
2 - Faça o código funcionar. PASSA
3 - Emiline redundâncias. REFATORA