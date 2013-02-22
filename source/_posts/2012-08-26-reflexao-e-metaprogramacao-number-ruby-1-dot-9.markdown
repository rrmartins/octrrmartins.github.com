---
layout: post
title: "Reflexão e Metaprogramação #Ruby 1.9 - Part I"
date: 2012-08-26 19:01
comments: true
categories: 
- Ruby API
- Integer
- String
- Hash
- Array
- Ruby
- each
- Ruby 1.9
- The Ruby Programming Language
---

<p>Hoje vamos continuar falando de <a href="http://www.ruby-doc.org/core-1.9.2/">Ruby</a>, é hora de nos aprofundar falando um pouco de <b>Reflexão e Metaprogramação</b>... Estranho para alguns, mas, veremos que é simples!</p>

<h1>Reflexão e Metaprogramação</h1>
<!-- more -->
Vimos que Ruby é uma linguagem muito dinâmica, você pode inserir novos métodos em classes em tempo de execução, criar apelidos
para métodos existentes, e até mesmo definir métodos em objetos individuais. Além disso, ele tem uma rica API para a reflexão.
Reflexão, também chamado de introspecção, significa simplesmente que um programa pode examinar seu estado e sua estrutura. Um
programa Ruby pode, por exemplo, obter a lista de métodos definidos pela classe `Hash`, consultar o valor de uma variável de
instância nomeada dentro de um objeto especificado, ou percorrer todos os objetos `Regexp` atualmente definidos pelo intérprete.
A API de reflexão, na verdade, vai além e permite que um programa para alterar o seu estado e estrutura. Um programa Ruby pode
definir dinamicamente variáveis chamadas, invocar métodos nomeados, e até mesmo definir novas classes e novos métodos.

API Reflexão Ruby, juntamente com a sua natureza geral, dinâmica, seu controle de estrutura de blocos iteradores, e a sintaxe 
dos seus parênteses opcionais sintaxe torna uma linguagem ideal para metaprogramação. Vagamente definida, metaprogramação está
escrevendo programas (ou frameworks) que ajudam a escrever programas. Para colocar de outra forma, a metaprogramação é um
conjunto de técnicas para estender a sintaxe de Ruby de uma forma que facilita a programação. Metaprogramação está intimamente
ligada à idéia de escrever linguagens específicas de domínio, ou `DSL's`. `DSL's` em Ruby normalmente usam invocações de métodos e
blocos, como se fossem palavras-chave em uma extensão de tarefas específicas para a linguagem.

Neste post começo com várias seções que introduzem a API de reflexão do Ruby. Esta API é surpreendentemente rica e consiste de
poucos métodos. Estes métodos são definidos, para a maior parte, pelo `Kernel`, `Module` e `Object`.

Enquanto você lê estas seções introdutórias, tenha em mente que reflexão não é, por si só, a metaprogramação. Metaprogramação
tipicamente estende a sintaxe ou o comportamento de Ruby, de alguma forma, e com frequência envolve mais do que um tipo de 
reflexão. Depois de introduzir a reflexão o núcleo da API de Ruby, neste post demonstrarei, por exemplo, técnicas comuns de
metaprogramação que usam essa API.

Note-se que este post aborda tópicos avançados. Você pode ser um programador Ruby produtivo sem nunca ler este post. Você pode
encontrar que é útil para ler os próximos post's deste primeiro livro, e depois retornar a este capítulo. Considere este
post uma espécie de exame final: se você entender os exemplos (especialmente os mais queridos no final), então você domina Ruby!

<!--more-->
<h3>Tipos, classes e módulos</h3>

Os métodos mais utilizados são aqueles mais reflexivos para determinar o tipo de um objeto que classe é uma instância e quais
métodos ele responde. Para rever:

``` ruby Metaprogramming
o.class
# Retorna a classe de do objeto 'o'.

c.superclass
# Retorna a superclasse de uma classe 'c'.

o.instance_of? c
# Determina se o objeto 'o.class == c'.

o.is_a? c
# Determina se 'o' é um exemplo de 'c', ou de qualquer das suas subclasses. Se 'c' for um módulo, este método testa se 'o.class' (ou qualquer um de seus ancestrais) inclui o módulo.

o.kind_of? c
# kind_of? é um sinônimo para is_a?.

c === o
# Para qualquer classe 'c' ou módulo, determina se 'o.is_a? (c)'

o.respond_to? nome
# Determina se o objeto 'o' tem um método público ou protegido com o nome especificado. Passar true como o segundo argumento para verificar métodos privados, também.
```

<h3>Ancestralidade e Módulos</h3>

Além desses métodos que você já viu, há mais alguns métodos relacionados reflexivos para determinar os ancestrais de uma 
classe ou módulo e para determinar quais os módulos que são incluídos por uma classe ou módulo. Esses métodos são fáceis de
entender quando demonstrado:

``` ruby Alguns demonstrações
module A; end                # Modulo vazio
module B; include A; end;    # Módulo B inclui A
class C; include B; end;     # Classe C inclui o módulo B

C < B                # => true: C inclui B
B < A                # => true: B inclui A
C < A                # => true
Fixnum < Integer     # => true: todos os fixnums são inteiros
Integer < Comparable # => true: inteiros são comparáveis
Integer < Fixnum     # => false: nem todos os números inteiros são fixnums
String < Numeric     # => nil: cordas não são números

A.ancestors          # => [A]
B.ancestors          # => [B, A]
C.ancestors          # => [C, B, A, Object, Kernel]
String.ancestors     # => [String, Enumerable, Comparable, Object, Kernel]
                     # Nota: em Ruby 1.9, String não é mais Enumerable

C.include?(B) # => true
C.include?(A) # => true
B.include?(A) # => true
A.include?(A) # => false
A.include?(B) # => false

A.included_modules # => []
B.included_modules # => [A]
C.included_modules # => [B, A, Kernel]
```

Este código demonstra `include?`, que é um método público de instância definido pela classe `Module`. Mas ele também possui duas
invocações do método `include` (Sem o sinal de interrogação), que é um método de instância particular de `Module`. Como um
método particular, pode apenas ser chamado implicitamente em si, o que limita a sua utilização para o corpo de uma definição de
`class` ou `module`. Este uso do método `include`, como se tratasse de uma palavra-chave, é um exemplo de metaprogramação no 
núcleo da sintaxe de Ruby.

Um método relacionado com o método `include`, particular é o público `Object.extend`. Este método estende um objeto por tornar
os métodos de instância de cada um dos módulos específicos em métodos `singleton` do objeto:

``` ruby Singleton
module Greeter; def hi; "hello"; end; end # módulo Greeter
s = "objeto de string"
s.extend(Greeter)       # Adicione "hello" como um método singleton para s
s.hi                    
String.extend(Greeter)  # Adicione "hello" como método de classe de String
String.hi 				# => "hello"
```

O método `Module.nesting` de classe não está relacionado com a inclusão do módulo ou ascendência, em vez disso, ele retorna um 
`array` que especifica o assentamento de módulos da localização atual. `Module.nesting[0]` é a classe atual ou módulo, `Module.nesting[1]` é o quem contém a `class` ou `module` e assim por diante:

```ruby Module e Class
module M
  class C
    Module.nesting   # => [M::C, M]
  end
end
```

<h3>Definindo Classes e Módulos</h3>

Classes e módulos são instâncias das classes de `Class` e `Module`. Como tal, você pode criá-los dinamicamente:

``` ruby Class e Module dinamicamente
M = Module.new      # Define um novo módulo M
C = Class.new       # Define uma nova classe C
D = Class.new(C) {  # Definir uma subclasse de C
  include M 		# inclui o módulo M
}
D.to_s 				# => "D": classe recebe o nome da constante por magia
```

Um recurso interessante do Ruby é que, quando um criado dinamicamente um módulo anónimo ou classe é atribuído a uma constante,
o nome dessa constante é usado como o nome do módulo ou classe (e é retornado pelo seu nome e método to_s).

É isso ai amigos...
Hora de dar um `break`... :)

Até o proximo!