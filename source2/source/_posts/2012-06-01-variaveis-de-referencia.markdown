---
layout: post
title: "Variáveis de Referência"
date: 2012-06-01 13:16
comments: true
categories:
- Array
- Hash
- String
- Ruby
- Ruby 1.8
- Ruby 1.9
- The Ruby Programming Language
---
<!--more-->
<p>Hoje vamos continuar falando de <a href="http://www.ruby-doc.org/core-1.9.2/">Ruby</a>, é hora de nos aprofundar falando um pouco de Variáveis de Referência</p>

<h1>Variáveis de Referência</h1>

Uma variável é simplesmente um nome para um valor. As variáveis ​​são criadas e os valores que lhes são atribuídos por expressões
de atribuição. Quando o nome de uma variável aparece num programa em qualquer lugar que não seja o lado esquerdo de uma atribuição, é uma
variável de referência à expressão e avaliado como o valor da variável:

```ruby Referência
one = 1.0 # Esta é uma atribuição de expressão
one # Essa referência de expressão da variável, que imprimi 1.0
```

Existem quatro tipos de variáveis ​​em Ruby, e regras <a href="http://pt.wikipedia.org/wiki/Item_lexical">lexicais</a> para governar seus nomes. Variáveis que começam com $ são variáveis ​​globais, visível ao longo de um programa Ruby. Variáveis ​​que começam com @ e @@ são
variáveis ​​de instância e variáveis ​​de classe, usado em programação orientada a objeto. E as variáveis ​​cujos nomes começam com um sublinhado
ou uma letra minúscula são variáveis ​​locais, definidas apenas dentro do atual método ou bloco.

Variáveis ​​sempre são simples, nomes não qualificados. Se um . ou :: aparece em uma expressão, em seguida, que a expressão é ou um
uma referência a uma constante ou uma invocação de método. Por exemplo, Math::PI é uma referência a uma constante, e a expressão
item.preco é uma invocação do método chamado preco (preço) sobre o valor realizado pela variável item.

O interpretador Ruby predefine um número de variáveis ​​globais quando ele é iniciado.

Até a próxima.. :)
