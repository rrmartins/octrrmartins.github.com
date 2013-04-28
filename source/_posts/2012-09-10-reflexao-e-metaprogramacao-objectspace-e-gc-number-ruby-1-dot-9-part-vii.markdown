---
layout: post
title: "Reflexão e Metaprogramação - ObjectSpace e GC - #Ruby 1.9 - Part VII"
date: 2012-09-10 23:26
comments: true
categories:
- Ruby API
- Integer
- String
- Object
- Kernel
- Ruby
- Hook
- Ruby 1.9
- The Ruby Programming Language
---
<!--more-->
<p>Hoje vamos continuar falando de <a href="http://www.ruby-doc.org/core-1.9.2/">Ruby</a>, é hora de continuar nos aprofundando um pouco mais de
<b>Reflexão e Metaprogramação</b> agora <b>ObjectSpace e GC</b>... Estranho para alguns, mas, veremos que é simples!</p>

<h1>ObjectSpace e GC</h1>

O módulo `ObjectSpace` define um punhado de métodos de baixo nível que pode ser ocasionalmente útil para depurar ou trabalhar com metaprogramação.
O método mais notável é `each_object`, um iterador que pode render cada objeto (ou a cada instância de uma classe especificada) que o intérprete sabe
sobre:

``` ruby ObjectSpace
# Imprima uma lista de todas as classes conhecidas
ObjectSpace.each_object(Class) {|c| puts c }
```

`ObjectSpace._id2ref` é o inverso da `Object.object_id:` leva um objeto como seu argumento ID e retorna ao objeto correspondente, ou levanta uma
RangeError se não há nenhum objeto com que ID.

`ObjectSpace.define_finalizer` permite o registo de uma `Proc` ou um bloco de código a ser chamado quando um objeto especificado é `garbage collected`.
Você deve ter cuidado ao registar um finalizador tal, no entanto, como o bloco não tem permissão de `finalizer` usar o objeto lixo coletado. Quaisquer
valores necessários para finalizar o objeto deve ser capturados no âmbito do bloco `finalizer`, de modo a que estejam disponíveis sem desreferência do
objeto. Use `ObjectSpace.undefine_finalizer` para excluir todos os blocos inscritos para um objeto `finalizer`.

O final método `ObjectSpace` é `ObjectSpace.garbage_collect`, que força o `garbage collected` de Ruby para ser executado. Funcionalidade de `garbage
collected` também está disponível através do módulo `GC`. `GC.start` é sinônimo de `ObjectSpace.garbage_collect`. `garbage collected` pode ser
desativado temporariamente com `GC.disable`, e ele pode ser ativado novamente com `GC.enable`.

A combinação do `_id2ref` e métodos `define_finalizer` permite a definição de "fracos" objetos de referência, que possuem uma referência a um valor sem
impedir o valor de ser coletado se tornar de outra forma inacessível. Consulte a classe `weakref` na biblioteca padrão (em lib/weakref.rb) para um exemplo.

Até o proximo post amigos...

Conhecimento nunca é d+ ! :)