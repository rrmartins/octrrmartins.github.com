---
layout: post
title: "Classe Objects em Ruby 1.9.2 - Part I"
date: 2012-05-19 10:23
comments: true
categories:
- Object
- Ruby
- Ruby 1.8
- Ruby 1.9
- The Ruby Programming Language
---
<!--more-->
<p>Continuando os estudos de Ruby, e a leitura do livro The Ruby Programming Language</p>

<p>Hoje vamos continuar falando de <a href="http://ruby-doc.org/core-1.9.3/Object.html">Objects</a>, é hora de nos aprofundar.</p>

<h1>Objetos</h1>

Ruby é uma forma muito pura de linguagem orientada a objetos: todos os valores são
objetos, e não há distinção entre tipos primitivos e tipos de objetos, como existem em muitos
outras línguas. Em Ruby, todos os objetos herdam de uma classe chamada
<a href="http://ruby-doc.org/core-1.9.3/Object.html">Object</a> e compartilhar os métodos definidos
por essa classe. Esta seção explica as características comuns de todos os objetos
em Ruby. Teremos algumas partes para falar desta classe tão poderosa, mas é muito importante para o entendimento de OO em Ruby.

<h3>Referências de objeto</h3>

Quando trabalhamos com objetos em Ruby, realmente estamos trabalhando com referências a objetos. Não é o
próprio objeto que manipula, mas uma referência a ele<a href="{{ root_url }}/blog/2012/05/19/classe-objects-em-ruby-1-dot-9-2/#referencia">[*]</a>.
Quando atribuir um valor a uma variável, não estamos copiando um objeto "em" variável que, nós
São apenas armazenar uma referência a um objeto para essa variável. Alguns
código deixa isso claro:

``` ruby Objects
s = "Ruby" # Criar um objeto String. Armazenar uma referência a ele em s.
t = s # Copiar a referência a t. s e t referem-se ambos para o mesmo objeto.
t[-1] = "" # Modificar o objeto através da referência t.
print s # Acesse o objeto modificado através de s. Prints "Esfregue".
t = "Java" # t agora se refere a um objeto diferente.
print s, t # Imprime "RubJava".
```

Quando você passar um objeto para um método em Ruby, é um objeto
referência que é passado para o método. Não é o objeto em si,
e não é uma referência para a referência ao objecto. Outra forma
dizer isso é que os argumentos do método são passados ​​por
valor e não por referência, mas
que os valores passados ​​são referências de objeto.

Porque as referências de objeto são passados ​​para os métodos, os métodos podem usar
essas referências para modificar o objeto subjacente. Essas modificações
são, então, visível quando o método retorna.

<h3>Valores imediatos</h3>

Nós dissemos que todos os valores em Ruby são objetos e todos os objetos
são manipulados por referência. Na implementação de referência, no entanto objetos Fixnum e Symbol são realmente "valores imediatos",
em vez de referências. Nenhuma destas duas classes tem métodos diferenciados, de modo que os
Objetos Fixnum e símbolo são imutáveis, o que significa que não há realmente nenhuma maneira de dizer que eles são manipulados por
valor, e não pela existência de valores de referencia.

A existência de valores imediatos deve ser considerado um detalhe de implementação. A única diferença prática entre
valores imediatos e valores de referência é que os valores imediatos
não pode ter métodos singleton definidas sobre eles.

<h3>Vida útil de objeto</h3>

As classes internas em Ruby descritas neste capítulo têm sintaxes literal e instâncias dessas classes são criadas simplesmente por
inclusão de valores literalmente em seu código. Objetos de outras classes precisam ser explicitamente criados,
e isso é feito na maioria das vezes com um método chamado de new:

``` ruby Class.new
myObject = MyClass.New
```

new é um método do Classe Class. Atribui na memória para manter o objeto new, então ele inicializa o estado do
recém alocados objetos "vazio", invocando seu método initialize. Os argumentos para new são passadas diretamente para inicializar.
A maioria das classes definem um método initialize para executar qualquer inicialização, sendo necessário para instâncias.

Os métodos new e initialize fornecer o padrão técnico para a criação de novas classes, mas classes também podem definir outros
métodos, conhecidos como "métodos de fábrica", que retornam instâncias.

Objetos Ruby não precisa ser desalocada explicitamente, como o fazem as linguagens C, C++, entre outras. Ruby usa uma técnica chamada
<a href="http://www.rubyinside.com.br/como-o-ruby-gerencia-memoria-e-faz-garbage-collection-3018">garbage collection</a> (coleta de lixo) automaticamente, para destruir objetos que não são mais necessários. Um objeto se torna um candidato para o garbage collection quando é
inacessível, quando não há referências restantes para o objeto com exceção de outros objetos inacessíveis.

O fato de que Ruby usa garbage collection, isso significa que programas em Ruby são menos suscetíveis a vazamentos de memória
que os programas escritos em linguagems que requerem objetos e memória para ser desalocada explicitamente e liberado.
Mas o garbage collection não significa que vazamentos de memória são impossíveis:
qualquer código que cria vida longa tem referências a objetos que de outro modo seria de curta duração pode ser uma fonte de
falhas de memória. Considere um hash usado como um cache. Se o cache não é podado usando algum tipo de algoritmo
menos utilizado recentemente, os objetos em cache permanecem acessíveis enquanto o hash em si é alcançável. Se o hash
é referenciado por uma variável global, então será acessível como desde que o interpretador Ruby está sendo executado.

<a href="referencia"></a>
	[*] -> Se você está familiarizado com C ou C++, você pode pensar de uma
    referência como um ponteiro: o endereço do objeto na memória. Ruby
    não utiliza ponteiros, no entanto. Referências em Ruby são opacas e
    internas para a implementação. Não há maneira de tirar o
    tratamento de um valor não referenciado, um valor, ou fazer ponteiro aritmético.


Um pouco de conceito de Objetos, logo teremos mais na pratica...

Até a proxima