---
layout: post
title: "Variáveis ​​não inicializadas"
date: 2012-06-02 11:30
comments: true
categories:
- Array
- Hash
- Variable
- String
- Ruby
- Ruby 1.8
- Ruby 1.9
- The Ruby Programming Language
---
<!--more-->
<p>Hoje vamos continuar falando de <a href="http://www.ruby-doc.org/core-1.9.2/">Ruby</a>, é hora de nos aprofundar falando um pouco de Variáveis ​​não inicializadas</p>

<h1>Variáveis ​​não inicializadas</h1>

Em geral, você deve sempre atribuir um valor para, ou inicializar as variáveis ​​antes de usá-las em expressões. Em algumas circunstâncias,
no entanto, Ruby lhe permitirá utilizar variáveis ​​que ainda não foram inicializadas. As regras são diferentes para diferentes tipos de variáveis:

<h4>Variáveis ​​de classe</h4>
	Variáveis ​​de classe deve sempre ter um valor atribuído a elas antes que serem utilizadas. Ruby levanta uma NameError se
	referir a uma variável de classe à qual nenhum valor foi atribuído.

<h4>Variáveis ​​de instância</h4>
	Se você se referir a uma variável de instância não inicializada, Ruby retorna nil. Considera-se má programação de
	contar com esse comportamento, no entanto. Ruby vai emitir um aviso sobre a variável não inicializada se você executá-la
	com a opção -w.

<h4>As variáveis ​​globais</h4>
	Variáveis ​​globais não inicializadas são como o exemplo de variáveis não inicializadas: elas igualam a nil, mas mostra
	um aviso quando	o Ruby é executado com o flag do -w.

<h4>As variáveis ​​locais</h4>
	Este caso é mais complicado que os outros, porque variáveis locais ​​não têm um caractere de pontuação como um prefixo.
	Este significa que variáveis ​​locais referênciam olhando apenas como uma invocação de método de expressões. Se o
	interpretador Ruby tem visto uma atribuição de uma variável local, ele sabe que é uma variável e não um método,
	e pode retornar o valor da variável. Se não tiver havido nenhuma atribuição, então o Ruby trata a expressão como uma
	invocação de método. Se nenhum método com esse nome existe,	Ruby levanta um NameError.

	Em geral, portanto, a tentativa de usar uma variável local antes de ter sido inicializada resulta em um erro. Existe
	uma	peculiaridade, uma variável passa a existir quando o interpretador Ruby vê uma expressão de atribuição para essa
	variável. Este é o caso, mesmo se essa atribuição não é realmente executada. A variável que existe, mas não foi
	atribuído um valor, é dado o valor nulo	como padrão. Por exemplo:

``` ruby Variaveis
a = 0.0 if false # atribuição Isso nunca é executado
puts a # Imprime nulo: a variável existe mas não é atribuída
puts b # NameError: nenhuma variável ou método chamado existe b
```

<a href="http://www.ruby-doc.org/docs/ProgrammingRuby/html/tut_classes.html">Variaveis Ruby 1.9.2</a>

Até a próxima.. :)
