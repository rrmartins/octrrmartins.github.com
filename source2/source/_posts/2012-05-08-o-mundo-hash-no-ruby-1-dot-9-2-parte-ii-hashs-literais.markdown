---
layout: post
title: "O Mundo Hash no Ruby 1.9.2 - Parte II - Hashs Literais"
date: 2012-05-08 22:28
comments: true
categories:
- Hash
- Hash Liiteral
- Literal
- Ruby
- Ruby 1.8
- Ruby 1.9
- The Ruby Programming Language
---
<!--more-->
<p>Continuando os estudos de Ruby, e a leitura do livro The Ruby Programming Language</p>

Na primeira parte eu fiz uma introdução do Hash:
-> <a href="{{root_url}}/blog/2012/05/08/o-mundo-hash-no-ruby-1-dot-9-2/">O Mundo Hash no Ruby 1.9.2 - Parte I</a>

Vamos lá!

Um literal de hash é escrita como uma lista separada por vírgulas de chave/valor de
pares, colocados dentro de chaves. Chaves e valores são separados
com um caractere de dois "arrow": '=>'. O objeto Hash criado anteriormente também pode ser
criado com o literal seguinte:


```ruby Hash Literal
números = {"um" => 1, "dois" => 2, "três" => 3}
```

Em geral, objetos <a href="http://www.ruby-doc.org/core-1.9.2/Symbol.html">Símbolos</a> trabalham mais eficientemente como chaves de hash de strings, fazem assim:

```ruby Hash Literal
números = {:um => 1,:dois => 2,:três => 3}
```

Os <a href="http://www.ruby-doc.org/core-1.9.2/Symbol.html">Símbolos</a> são imutáveis, escrito como
prefixados identificadores, que serão explicados em maior detalhe em um outro post.

Ruby 1.8 permite vírgulas em lugar de setas, mas isso não teve a sintaxe substituída, mais suportado no Ruby 1.9:

```ruby Hash Literal
 numeros = {:um, 1, :dois, 2, :tres, 3} # Igual, mas mais difícil de ler
```

Tanto Ruby 1.8 quanto no Ruby 1.9 permite uma única vírgula à direita do
final da lista de chave / valor:

```ruby Hash Literal
números = {: um => 1,: dois => 2} # vírgula extra ignorado
```

Ruby 1.9 suporta muito útil e sucinto a sintaxe literal de Hash
quando as chaves são símbolos. Neste caso, move-se para o fim da chave de hash e substitui a seta:

```ruby Hash Literal
números = {um: 1, dois: 2, tres: 3}
```

Note-se que pode não haver qualquer espaço entre a chave Hash
identificador e dos dois pontos.

Até breve!