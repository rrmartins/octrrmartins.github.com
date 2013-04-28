---
layout: post
title: "Symbols - Ruby 1.9.2"
date: 2012-05-16 21:37
comments: true
categories:
- Symbol
- Ruby
- Ruby 1.8
- Ruby 1.9
- The Ruby Programming Language
---
<!--more-->
<p>Continuando os estudos de Ruby, e a leitura do livro The Ruby Programming Language</p>

<p>Hoje vamos continuar falando de Symbol, é hora de nos aprofundar.</p>

<h2>Symbol</h2>

Uma implementação típica de um interpretador Ruby mantém um símbolo em tabela
que ele armazena os nomes de todas as classes, métodos e variáveis ​​que ele conhece.
Isso permite que tal intérprete evite mais comparações de string: refere-se a nomes de métodos (por exemplo) pelo sua
posição na presente tabela de símbolos. Isso transforma uma string relativamente pesada em uma certa operação, e relativamente leve com operação de números inteiro.


Esses símbolos não são puramente interno para o intérprete, pois eles podem também ser usado por
programas Ruby. Um objeto de Symbol refere-se a um símbolo. Um símbolo literal é escrito prefixando um identificador ou uma string com um
dois pontos:

``` ruby Symbol
:symbol # Symbol Um literal
:"symbol" # O mesmo literal
:'another long symbol' "# são úteis para símbolos com espaços
s = "string"
sym = :"#{s}" # O Symbol :string
```

Símbolos têm também uma sintaxe literal %s que permite delimitadores arbitrários da mesma maneira
que %q e %Q pode ser usado para strings:

``` ruby Symbol
%s["] # O mesmo que: '"'
```

Os símbolos são usados ​​frequentemente para se referir a nomes de métodos no reflexivo
código. Por exemplo, suponha que queremos para saber se algum objeto tem um método each:

``` ruby Symbol
o.respond_to? :each
```

Aqui está outro exemplo. Ele testa se um determinado objeto responde a um método especificado, e, em caso afirmativo, invoca o método:

``` ruby Symbol
nome = :size
if o.respond_to? nome
  o.send (nome)
end
```

Você pode converter uma String para um Symbol utilizando os métodos intern ou to_sym. E você pode converter um Symbol de volta para uma String com o método to_s ou sue alias id2name:

``` ruby Symbol
str = "string" # Comece com uma string
sym = str.intern  # Converter para um símbolo
sym = str.to_sym # Outra maneira de fazer a mesma coisa
str = sym.to_s # converter de volta para uma string
str = sym.id2name # Outra maneira de fazê-lo
```

Duas seqüências podem ter o mesmo conteúdo e ainda ser completamente de objetos distintos. Este nunca é o caso com símbolos.
Duas strings com o mesmo conteúdo irá tanto converter para exatamente o mesmo objeto symbol. Dois objetos distintos Symbols terá sempre conteúdo diferente.

Sempre que você escrever código que usa string não para o seu conteúdo textual,
mas como uma espécie de identificador único, considere o uso de symbols em vez disso.
Ao invés de escrever um método que espera um argumento para ser ou a string de "AM" ou "PM", por exemplo, você poderia escrevê-lo para
esperar o símbolo :AM ou o símbolo :PM. Comparando-se dois objetos Symbols de igualdade, é muito mais rápido
de comparar duas strings para a igualdade. Por esta razão, os símbolos são geralmente preferido para strings como chaves de hash.

No Ruby 1.9, a classe símbolo define um número de métodos String, como length, o size, os operadores de comparação, e mesmo os operadores
[] e =~. Isto faz com que os símbolos sejam um pouco permutável com string e permite a sua utilização como uma espécie de imutável
(E não garbage-collected) string.


É isso ai amigos até a proxima.. :D