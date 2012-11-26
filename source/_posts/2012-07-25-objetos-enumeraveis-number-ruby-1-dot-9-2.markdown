---
layout: post
title: "Objetos Enumeráveis #Ruby 1.9.2"
date: 2012-07-25 22:50
comments: true
categories:
- Ruby API
- Integer
- String
- Array
- Ruby
- Enumerable
- each
- Ruby 1.9
- The Ruby Programming Language
---

<p>Hoje vamos continuar falando de <a href="http://www.ruby-doc.org/core-1.9.2/">Ruby</a>, é hora de nos aprofundar falando um pouco de `Objetos Enumeráveis`... Estranho para alguns, mas, veremos que é simples!</p>

<h1>Objetos Enumeráveis</h1>

`Array`, `Range`, `Hash`, e um número de outras classes definem um iterador `each` que passa cada elemento da
coleção para o bloco associado. Este é talvez o mais comumente usado iterador em Ruby, o loop só funciona para
iterar sobre objetos que têm o método `each`. Exemplos de iteradores `each`:
<!-- more -->
```ruby Metodo each
[1,2,3].each {|x| print x }   # => prints "123"
(1..3).each  {|x| print x }   # => prints "123" Same as 1.upto(3)
```

O iterador `each` não é só para as classes tradicionais "de estrutura de dados". Classes `IO` Ruby definem um
iterador `each` que cada linha de texto lido do objeto produz `Input/Output`. Assim, você pode processar as linhas
de um arquivo em Ruby com um código como esse:

```ruby Metodo each para File
File.open(filename) do |f|       # Abrir arquivo nomeado, passando f como parametro
  f.each {|line| print line }    # Imprimir f em cada linha
end
```

A maioria das classes que definem um método `each` também incluem o módulo `Enumerable`, que define um número de
iteradores mais especializados que são implementados em cima do método `each`. Um iterador é útil tal `each_with_index`, o que nos permite adicionar a linha de numeração para o exemplo anterior:

``` ruby Metodo each com Index
File.open(filename) do |f|
  f.each_with_index do |line,number|
    print "#{number}: #{line}"
  end
end
```

Alguns dos iteradores mais comumentes utilizados são os métodos `Enumerable`, `collect`, `select`, `reject`, e
`inject`. O método `collect` (também conhecido como `map`) executa o seu bloco associado para cada elemento do objeto 
enumerável, e coleta os valores de retorno dos blocos em um `array`:

```ruby Metodo Collect
quadrados = [1,2,3].collect {|x| x*x}   # => [1,4,9]
```

O método `select` invoca o bloco associado para cada elemento no objeto enumerável, e retorna uma matriz de
elementos para a qual o bloco retorna um outro valor `false` ou `nil`. Por exemplo:

```ruby Metodo Select
nivela = (1..10).select{|x| x%2 == 0} # => [2,4,6,8,10]
```

O método `reject` é simplesmente o oposto de `select`, ele retorna uma matriz de elementos para a qual o bloco
retorna `nil` ou `false`. Por exemplo:

```ruby Metodo reject
odds = (1..10).reject{|x| x%2 == 0} # => [1,3,5,7,9]
```

O método `inject` é um pouco mais complicado do que os outros. Ele invoca o bloco associado com dois argumentos. 
O primeiro argumento é um valor acumulado de algum tipo das iterações anteriores. O segundo argumento é o próximo
o objeto enumerável. O valor de retorno do bloco é o primeiro argumento bloqueado para a próxima iteração, ou torna-
se o valor de retorno do iterador após a última iteração. O valor inicial da variável acumula ou é o argumento de
`inject`, se houver um, ou o primeiro elemento do objeto enumerável. (Neste caso, o bloco é invocado. Apenas uma vez durante os primeiros dois elementos). Exemplos para `injects` mais claros:

```ruby Metodo Inject
data = [2, 5, 3, 4]
sum = data.inject{|sum, x| sum + x }      # => 14    (2+5+3+4)
floatprod = data.inject(1.0){|p,x| p*x }  # => 120.0 (1.0*2*5*3*4)
max = data.inject{|m,x| m>x ? m : x }     # => 5     (elemento maior)
```


Veja <a href="http://ruby-doc.org/core-1.9.2/Enumerable.html">Objetos Enumeráveis</a> ​​para obter mais detalhes sobre o `Módulo Enumerable` e a seus iteradores.

Até a proxima amigos...