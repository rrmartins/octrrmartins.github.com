---
layout: post
title: "unless #Ruby 1.9"
date: 2012-07-05 01:57
comments: true
categories: 
- Enumerable
- Variable
- String
- Array
- Ruby
- Ruby 1.8
- Ruby 1.9
- The Ruby Programming Language
---

<p>Hoje vamos continuar falando de <a href="http://www.ruby-doc.org/core-1.9.2/">Ruby</a>, é hora de nos aprofundar falando um pouco de `unless`... Estranho para alguns, mas, veremos que é simples!</p>

<h1>Unless</h1>

`unless`, como uma declaração ou um modificador, é o oposto do `case`: ele executa o código apenas se uma expressão 
associada é avaliada como `false` ou `nil`. Sua sintaxe é como `if`, exceto que as cláusulas `elsif`, que não são
permitidas:

``` ruby Declaração unless
# Uma maneira, declaração unless
unless condition
  code
end

# Duas vias da declaração unless
unless condition
  code
else
  code
end

# modificador unless
code unless condition
```

A declaração `unless`, como a declaração `if`, exige que a condição e o código são separados por uma mudança de linha,
uma vírgula, ou a palavra-chave `then`. Também como `if`, as declarações `unless` são expressões e retornam o valor do
código que são executados, ou `nil` se executar nada:

``` ruby Declaração unless
# Chama o método to_s sobre o objeto, a menos que o objeto 'o' for nulo
s = unless o.nil?                        # Nova linha de separação
  o.to_s
end

s = unless o.nil? then o.to_s end        # separador então
```

Para uma única linha condicionais como esta, a forma do modificador `unless` é geralmente mais clara:

``` ruby Unless
s = o.to_s unless o.nil?
```

Ruby não tem nenhum equivalente da cláusula `elsif` para a condicional `unless`. Você ainda pode escrever um multiway da instrução `unless`, no entanto, se você está disposto a ser um pouco mais detalhado:

``` ruby unless
unless x == 0
  puts "x is not 0"
else
  unless y == 0
    puts "y is not 0"
  else
    unless z == 0
      puts "z is not 0"
    else
      puts "all are 0"
    end
  end
end
```

É isso ai amigos! 

Para todos aqueles que não sabiam ao certo o que seria o `unless`, e que tipo de monstro é isso..

Esta ai..

Até a proxima!