---
layout: post
title: "Iteradores Numéricos #Ruby 1.9.2"
date: 2012-07-14 22:52
comments: true
categories: 
- Ruby API
- Integer
- String
- Array
- Ruby
- Ruby 1.8
- Ruby 1.9
- The Ruby Programming Language
---

<p>Hoje vamos continuar falando de <a href="http://www.ruby-doc.org/core-1.9.2/">Ruby</a>, é hora de nos aprofundar falando um pouco de `Iteradores Numéricos`... Estranho para alguns, mas, veremos que é simples!</p>

<h1>Iteradores Numéricos</h1>
<!-- more -->
O núcleo do `Ruby API` fornece um número de iteradores padrão. Os métodos de loops do Kernel se comporta como um
loop infinito, executando seu bloco associado repetidamente até que o bloco executa um `return`, `break`, ou outra
declaração que sai do laço.

A classe `Integer` define três iteradores usados. O método `upto` chama seu bloco associado uma vez para cada número
inteiro entre o número inteiro no qual ele é invocado e o número inteiro que é passado como um argumento. 
Por exemplo:

``` ruby upto
4.upto(6){|x| print x} # => prints "456"
```

Como você pode ver, `upto` intera cada número inteiro para o bloco associado, e inclui tanto o ponto de partida e o
ponto final na iteração. Em geral, `n.upto(m)` é executado o bloco `m-n +1` vezes.

O método `downto` é como `upto` mas itera a partir de um maior número para um número menor.

Quando o método `Integer.times` é chamado no `n` inteiro, invoque o seu bloco de `n vezes`, passando os valores de 
`0 a n-1` em iterações sucessivas. Por exemplo:

``` ruby times
3.times {|x| print x }    # => prints "012"
```

Em geral, é `n.times` equivalente a `0.upto(n-1)`.

Se você quer fazer uma iteração numérica usando um ponto flutuante de números, você pode usar o método `step` mais
complexo definido pela classe numérica. O iterador a seguir, por exemplo, começa a 0 e itera em passos de 0.1 até 
atingir `Math::PI`:

``` ruby metodo step
0.step(Math::PI, 0.1){|x| puts Math.sin(x)}
``` 

Até a proxima!
