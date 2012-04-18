---
layout: post
title: "Classe Objects em Ruby 1.9.2 - Part IV"
date: 2012-05-20 13:01
comments: true
categories: 
- Comparable
- Object
- Ruby
- Ruby 1.8
- Ruby 1.9
- The Ruby Programming Language
---

<p>Hoje vamos continuar falando de <a href="http://ruby-doc.org/core-1.9.3/Object.html">Objects</a>, é hora de nos aprofundar.</p>

<h1>Objetos</h1>

<h3>Ordem de Object</h3> 

Praticamente todas as classes podem definir o método ==, sendo útil para testar as suas instâncias para a igualdade. Algumas 
classes também podem definir uma ordenação. Ou seja: para quaisquer duas instâncias de uma classe, as duas instâncias devem ser iguais, 
ou uma instância deve ser "menos que" a outra. Numbers são das classes mais óbvias para que tal ordenação seja definido. 
String são também ordena, de acordo com o numéro de ordenação dos códigos de caracteres que compõem as strings. Se uma classe define 
uns casos de pedido, em seguida, a classe pode ser comparada e classificada.
<!--more-->
Em Ruby, classes definem um ordenação através da implementação do operador <=>. Este operador deve retornar -1 se o operando da esquerda 
é menor que o operando da direita, 0 se os dois operandos são iguais, e 1 se o operando esquerdo é maior que o operando direito. 
Se os dois operandos não podem ser	 significativamente comparados (se o operando direito é de uma classe diferente, por exemplo), 
em seguida, o operador deve retornar nil:

``` ruby Operador <=>
1 <=> 5 # -1
5 <=> 5 # 0
9 <=> 5 # 1
"1" <=> 5 # nil: inteiros e strings não são comparáveis
```

O operador <=> é tudo que é necessário para comparar os valores. Mas não é particularmente intuitivo. Assim, as classes que definem 
este operador tipicamente também incluir o Módulo <a href="http://ruby-doc.org/core-1.9.2/Comparable.html">Comparable</a> como um mixin. 
(Módulos e mixins são abordados em Módulos como Mixins). O mixin Comparable define o seguinte operador em termos de <=>:

	< 	-	Menor que
	<=	-	Menor ou igual
	==	-	Igual
	>=	-	Maior ou igual
	>	-	Maior que

<a href="http://ruby-doc.org/core-1.9.2/Comparable.html">Comparable</a> não define o operador !=; o Ruby automaticamente define o 
operador como a negação do operador ==. Além destes operadores de comparação, <a href="http://ruby-doc.org/core-1.9.2/Comparable.html">Comparable</a> também define um método útil de comparação com o between? :

``` ruby Comparable between?
1.between?(0,10) # verdadeiro: 0 <= 1 <= 10
```

Se o operador <=> retornar nil, todos os operadores de comparação dele derivados retornam falso. O especial Float com valor NaN é um exemplo:

``` ruby Float NaN
nan = 0.0/0.0; # zero dividido por zero não é um número
nan < 0 # false: não é menor que zero
nan > 0 # false: não é maior que zero
nan == 0 # false: não é igual a zero
nan == nan # false: não é mesmo igual a si mesmo!
nan.equal?(nan) # isso é verdade, claro
```

Observe que a definição de <=> e incluindo o módulo <a href="http://ruby-doc.org/core-1.9.2/Comparable.html">Comparable</a> define um operador == para o sua classe. Algumas classes que definem o seu próprio operador ==, normalmente quando eles podem
implementar esta forma mais eficiente do que um teste de igualdade com base no operador <=>. É possível definir classes que 
implementam diferentes noções de igualdade em seus operadores == e <=>. Uma classe pode fazer comparações de string case-sensitive 
para o operador ==, por exemplo, mas, em seguida, fazem comparações de maiúsculos e minúsculos com <=>, de modo que as instâncias da classe
se classificam com mais naturalidade. Em geral, porém, é melhor se <=> retorna 0 se e somente se == retorna true.

É isso amigos... o proximo post vamos conversar um pouco de Conversões de Object's.

Até Mais..