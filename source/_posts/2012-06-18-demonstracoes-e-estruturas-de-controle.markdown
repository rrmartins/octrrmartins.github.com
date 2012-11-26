---
layout: post
title: "Demonstrações e Estruturas de controle - Part I #Ruby"
date: 2012-06-18 08:43
comments: true
categories: 
- if
- else
- elsif
- Variable
- String
- Array
- Ruby
- Ruby 1.8
- Ruby 1.9
- The Ruby Programming Language
---

<p>Hoje vamos continuar falando de <a href="http://www.ruby-doc.org/core-1.9.2/">Ruby</a>, é hora de nos aprofundar falando um pouco de Demonstrações e estruturas de controle</p>

<h1>Demonstrações e estruturas de controle</h1>

Considere o programa Ruby seguinte. Acrescenta dois números passados ​​para na linha de comando e imprime a soma:
<!-- more -->
``` ruby Programa Simples
x = ARGV[0].to_f # Converter primeiro argumento para um número
y = ARGV[1].to_f # Converter segundo argumento para um número
soma = x + y # Adicione os argumentos
puts soma # Imprimir a soma
```

Este é um programa simples que consiste essencialmente em invocar variável de atribuição e método. O que o torna
particularmente simples é sua execução puramente seqüencial. As quatro linhas de código são executadas uma após a outra
sem ramificação ou repetição. É um programa raro que pode ser tão simples.


<h3>Condicionais</h3>

A estrutura de controle mais comum, em qualquer linguagem de programação, é a condicional. Esta é uma forma de dizer ao
computador para condicionalmente executar alguns códigos: para executar ele, só se alguma condição for satisfeita. 
A condição é uma expressão, se for avaliada como qualquer valor diferente de falso ou nulo, então a condição é satisfeita.

Ruby tem um vocabulário rico para expressar condicionais. Algumas sintaxes são descritas abaixo. Ao escrever código Ruby,
você pode escolher o que parece mais elegante para a tarefa.
<!--more-->

<h5>if</h5>

Ele é a mais simples das condicionais. Em sua forma mais simples, parece que isso:

``` ruby Condicional IF
if expression
  code
end
```

O código entre `if` e `end `é executado se (e somente se) o `expression` é avaliada como algo diferente que `falso` ou 
`nulo`. O código deve ser separado a partir da expressão com uma nova linha ou ponto e vírgula ou a palavra-chave, em
seguida. Aqui estão duas maneiras de escrever a mesma condicional `if` de forma simples:

```ruby Condicional IF
# Se x é menor que 10, incrementá-lo
if x < 10 # nova linha de separação
  x + = 1
end
if x < 10 then x += 1 end # the separato
```

Você também pode usar `then` como o símbolo de separação, e segui-lo com uma nova linha. Isso torna o seu código robusto.

``` ruby Condicional IF
if x < 10 then
  x + = 1
end
```

Os programadores que estão acostumados a C, ou linguagens cuja sintaxe é derivada de C, deve observar duas coisas
importantes sobre if no Ruby:

* Parênteses não é necessário (e normalmente não utilizado) em torno da expressão condicional. O ponto e vírgula, quebra
de linha, ou então palavra-chave serve para delimitar a expressão.

* A palavra-chave `end` é exigida, mesmo quando o código a ser executado condicionalmente consiste de uma única instrução.
A forma modificador do `if`, descrito abaixo, fornece uma maneira para gravar condicionais simples sem a palavra-chave `end`.


<h5>else</h5>

Uma declaração `if` pode incluir uma cláusula `else` para especificar o código para ser executado se a condição não é
`true`:

``` ruby Condicional else
if expression
  code
else
  code
end
```

O código entre `if` e `else` é executado se `expression` é avaliada como qualquer outra coisa que `false` ou `nil`. Caso 
contrário (se expression é `false` ou `nil`), o código entre o `else` e o `end` é executado. Como na forma simples 
de `if`, na expressão deve ser separado do código que o segue por uma nova linha, um ponto e vírgula, ou a palavra-chave `then`. As palavras-chave `else` e `end`, totalmente delimitam o segundo pedaço de código, e sem novas linhas ou
delimitadores adicionais são exigido.

Aqui é um exemplo de um condicional que inclui uma cláusula `else`:

``` ruby Condicional if - else
if dados # Se o array existe
  dados << x # em seguida, acrescentar um valor a ela.
else # Caso contrário ...
  data = [x] # criar uma nova matriz que contém o valor.
end # Este é o fim do condicional
```

<h5>elsif</h5>

Se você quiser testar mais de uma condição dentro de uma condicional, você pode adicionar um ou mais cláusulas `elsif`
entre um `if` e um `else`. `elsif` é uma forma abreviada de `else if`. Note que há apenas um `else` em `elsif`. A condicional `elsif` usando parecido com este:

```ruby Expressão elsif
if expression1
  code1
elsif expression2
  code2
      .
      .
      .
elsif expressionN
  codeN
else
  code
end
```

Se `expression1` for avaliado não sendo `false` ou `nil`, então `code1` é executado. Caso contrário, `expression2` é 
avaliada. Se for outra coisa senão `false` ou `nil`, então code2 é executado. Este processo continua até que uma expressão
é avaliada como algo diferente de `false` ou `nil`, ou até que todas as cláusulas `elsif` foram testadas. Se a expressão
associada com a última cláusula `elsif` for `false` ou `nil`, e da cláusula `elsif` é seguido por uma cláusula `else`, em
seguida, o código entre `else` e no `end` é executado. Se nenhuma cláusula mais está presente, em seguida, nenhum código é
executado.

`elsif` é como se: a expressão deve ser separada do código por uma nova linha, uma vírgula ou uma palavra-chave, em seguida
. Aqui é um exemplo de uma multi condicional usando `elsif`:

```ruby Condicional elsif
if x == 1
  name = "um"
elsif x == 2
  name = "dois"
elsif x == 3 then name = "três"
elsif x == 4; name = "quatro"
else
  name = "muitos"
end
```

<h5>Valor de retorno</h5>

Na maioria das linguagens, a condicional `if` é uma afirmação. Em Ruby, no entanto, tudo é uma expressão, mesmo as
estruturas de controle que são comumente chamadas de declarações. O valor `return` de uma "declaração" `if`, é o valor da
última expressão no código que foi executado, ou `nil` se nenhum bloco de código foi executado.

O fato de que, as declarações `if` devolvem um valor significa que, por exemplo, a muilti condicional mostrado
anteriormente pode ser elegantemente reescrito como este abaixo:

``` ruby Valor de Retorno
name = if x == 1 then "um"
       elsif x == 2 then "dois"
       elsif x == 3 then "três"
       elsif x == 4 then "quatro"
       else "muitos"
       end
```

É isso aí amigos..

Bons estudos e até a proxima! :D