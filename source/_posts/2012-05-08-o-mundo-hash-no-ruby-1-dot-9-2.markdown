---
layout: post
title: "O Mundo Hash no Ruby 1.9.2 - Parte I"
date: 2012-05-08 21:27
comments: true
categories: 
- Hash
- Ruby
- Ruby 1.8
- Ruby 1.9
- The Ruby Programming Language
---

<p>Continuando os estudos de Ruby, e a leitura do livro The Ruby Programming Language</p>

Um hash é uma estrutura de dados que mantém um conjunto de objetos conhecidos
como chaves, e associa um valor com cada chave. Hashs são também conhecidos como mapas porque mapeam as chaves para
valores. Eles às vezes são chamados de matrizes associativas, porque eles
associam valores com cada uma das chaves, e pode ser pensado como matrizes
em que o índice da matriz pode ser qualquer objeto em vez de um inteiro. um
exemplo torna isso mais claro:

```ruby Hash
# Este hash irá mapear os nomes dos dígitos para os dígitos se
números = Hash.new # Criar um novo objeto, vazio de hash
números["um"] = 1 # Mapa do String "um" para o Fixnum 1
números["dois"] = 2 # Note que estamos usando a notação de matriz aqui
números["três"] = 3

soma = números["um"] + números["dois"] # soma os valores e resulta em "3"
```

Esta introdução à sintaxe hashes de hash documentos Ruby literal
e explica os requisitos para um objeto a ser usado como uma chave hash.
Mais informações sobre a API definida pela classe Hash é fornecido em <a href="http://www.ruby-doc.org/core-1.9.2/Hash.html">Hashes</a>.

Em breve, mais conteudo de Hash!