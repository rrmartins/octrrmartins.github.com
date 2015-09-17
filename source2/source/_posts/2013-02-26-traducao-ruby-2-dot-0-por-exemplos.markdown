---
layout: post
title: "[Tradução] Ruby 2.0 por Exemplos"
date: 2013-02-26 22:54
comments: true
categories:
- Ruby
- Tradução
- Ruby 2.0
- Marc-Andre
-
---
<!--more-->
Olá amigos,

Tudo tranquilo?

Estava conversando com o [Marc-Andre Lafortune][1], do blog [Marc Andre][2], sobre a nova versão do [Ruby 2.0][3], e ele me permitiu traduzir o [post dele][4] e ai vai.. :)

Antes de começar a tradução, gostaria de lembrar do que disse o proprio criador do #Ruby no [twitter][5]:

{% blockquote @yukihiro_matz https://twitter.com/yukihiro_matz/status/305334327938519040 %}

20 years has passed since I started developing Ruby. I really appreciate your support from the great Ruby community. Thank you!

{% endblockquote %}

Neste trecho, [Matz][6] diz que desde quando começou a desenvolver o Ruby já se passou 20 anos e agradece a toda a comunidade que ajuda direta ou indiretamente.

Vamos a tradução... :D

- - -
Tradução
- - -


Um rápido resumo de alguns dos novos recursos do [Ruby 2.0.0][3]:

###Alterações na Linguagem

``` ruby
	# Ruby 1.9:
  # (Do action_view/helpers/text_helper.rb)
def cycle(first_value, *values)
  options = values.extract_options!
  name = options.fetch(:name, 'default')
  # ...
end

# Ruby 2.0:
def cycle(first_value, *values, name: 'default')
  # ...
end

# ATENÇÃO: Não exatamente idêntica, como palavras-chave são aplicadas:
cycle('odd', 'even', nme: 'foo')
# => ArgumentError: unknown keyword: nme

# Para obter o mesmo resultado e melhor exato:
def cycle(first_value, *values, name: 'default', **ignore_extra)
  # ...
end

```

Isso faz com que as definições de método seja muito flexível. Em resumo:

``` ruby
def name({required_arguments, ...}
         {optional_arguments, ...}
         {*rest || additional_required_arguments...} # Você Sabia?
         {keyword_arguments: "with_defaults"...}
         {**rest_of_keyword_arguments}
         {&block_capture})
```

Em [Ruby 2.0.0][3], argumentos devem ter padrões, ou então deve ser capturado por `**extra` no final. Próxima versão [permitirá][7] argumentos obrigatórios, por exemplo, `def hello(optional: 'default', required:)`, mas há [maneiras de fazer isso agora][8].

Padrões, para parâmetros opcionais ou argumentos de palavras-chave, pode ser na maior parte qualquer expressão, incluindo chamadas de método para o objeto atual e pode usar os parâmetros anteriores.

Um exemplo complexo que mostra a maior parte deste:

``` ruby
class C
  def hi(needed, needed2,
         maybe1 = "42", maybe2 = maybe1.upcase,
         *args,
         named1: 'hello', named2: a_method(named1, needed2),
         **options,
         &block)
  end

  def a_method(a, b)
    # ...
  end
end

C.instance_method(:hi).parameters
# => [ [:req, :needed], [:req, :needed2],
#      [:opt, :maybe1], [:opt, :maybe2],
#      [:rest, :args],
#      [:key, :named1], [:key, :named2],
#      [:keyrest, :options],
#      [:block, :block] ]
```

[Bug conhecido][9]: não é atualmente possível ignorar opções extras sem citar o argumento `**`.

###Criação de lista de Symbol

Maneira fácil de criar listas de símbolos com `%i` e `%I` (onde i é para uso interno):

```ruby
# Ruby 1.9:
KEYS = [:foo, :bar, :baz]

# Ruby 2.0:
KEYS = %i[foo bar baz]
```

###Codificação padrão é UTF-8

Nenhum comentário magico é necessário caso a codificação for UTF-8.

```ruby
# Ruby 1.9:
# encoding: utf-8
# ^^^ previous line was needed!
puts "❤ Rodrigo Martins ❤"

# Ruby 2.0:
puts "❤ Rodrigo Martins ❤"
```

###Variáveis ​​não utilizadas pode começar com _

Você sabia que Ruby pode avisá-lo sobre as variáveis ​​utilizadas?

```ruby
# Qualquer versão do Ruby, com aviso em:
ruby -w -e "
  def hi
    hello, world = 'hello, world'.split(', ')
    world
  end"
# => warning: assigned but unused variable - hello
```

A maneira de evitar o aviso era usar `_`. Agora podemos usar qualquer nome de variável começando com um underscore:

```ruby
# Ruby 1.9
ruby -w -e "
  def foo
    _, world = 'hello, world'.split(', ')
    world
  end"
# => no warning

# Ruby 2.0
ruby -w -e "
  def hi
    _hello, world = 'hello, world'.split(', ')
    world
  end"
# => no warning either
```

##Mudanças das Classes Principais

###Prepend

[Module#prepend][10] insere um módulo no início da cadeia de ligação. Pode muito bem substituir por `alias_method_chain`.

```ruby
# Ruby 1.9:
class Range
  # Do active_support/core_ext/range/include_range.rb
  # Estende o padrão Range#include? para suportar comparações de range
  def include_with_range?(value)
    if value.is_a?(::Range)
      # 1...10 inclue 1..9 mas não inclue 1..10.
      operator = exclude_end? && !value.exclude_end? ? :< : :<=
      include_without_range?(value.first) && value.last.send(operator, last)
    else
      include_without_range?(value)
    end
  end

  alias_method_chain :include?, :range
end

Range.ancestors # => [Range, Enumerable, Object...]

# Ruby 2.0
module IncludeRangeExt
  # Estende o padrão Range#include? para suportar comparações de range
  def include?(value)
    if value.is_a?(::Range)
      # 1...10 inclue 1..9 mas não inclue 1..10.
      operator = exclude_end? && !value.exclude_end? ? :< : :<=
      super(value.first) && value.last.send(operator, last)
    else
      super
    end
  end
end

class Range
  prepend IncludeRangeExt
end

Range.ancestors # => [IncludeRangeExt, Range, Enumerable, Object...]
```

###Refinamentos [experimental]

No Ruby 1.9, se você usa um metodo `alias_method_chain`, a nova definição ocorre em todos os lugares. Em [Ruby 2.0.0][3], você pode fazer esse tipo de mudança apenas para si mesmo utilizando o [Module#refine][11]:

```ruby
# Ruby 2.0
module IncludeRangeExt
  refine Range do
    # Estende o padrão Range#include? para suportar comparações de range
    def include?(value)
      if value.is_a?(::Range)
        # 1...10 inclue 1..9 mas não inclue 1..10.
        operator = exclude_end? && !value.exclude_end? ? :< : :<=
        super(value.first) && value.last.send(operator, last)
      else
        super
      end
    end
  end
end

def test_before(r)
  r.include?(2..3)
end
(1..4).include?(2..3) # => false (comportamento padrão)

# Agora liga o refinamento:
using IncludeRangeExt

(1..4).include?(2..3) # => true  (comportamento refinado)

def test_after(r)
  r.include?(2..3)
end
test_after(1..4) # => true (definido depois de usar, o comportamento tão refinado)

3.times.all? do
  (1..4).include?(2..3)
end # => true  (comportamento refinado)

# Mas a versão refinada acontece apenas para chamadas definidas após o uso:
test_before(1..4) # => false (definido anteriormente, não afetado)
require 'some_other_file' # => não afetado, usará o comportamento predefinido

# Note:
(1..4).send :include?, 2..3 # => false (por agora, envio ignora refinamentos)
```
Spec completo está [aqui][12] e está sujeita a mudanças em versões posteriores. Discussão mais aprofundada [aqui][13].

###Enumeradores Lazy

Um [Enumerable][14] pode ser transformado em um lazy com o novo método [Enumerable#lazy][15]:

```ruby
# Ruby 2.0:
lines = File.foreach('a_very_large_file')
            .lazy # por isso só ler as partes necessárias!
            .select {|line| line.length < 10 }
            .map(&:chomp)
            .each_slice(3)
            .map {|lines| lines.join(';').downcase }
            .take_while {|line| line.length > 20 }
  # => Enumerador lazy, ainda não executa nada
lines.first(3) # => Lê o arquivo até que ele retornar 3 elementos
               # ou até que um elemento de length <= 20 é
               # retornado (por causa da take_while)

# Para consumir o enumerável:
lines.to_a # ou...
lines.force # => Lê o arquivo e retorna uma array
lines.each{|elem| puts elem } # => Lê o arquivo e imprime os elementos resultantes
```

Note-se que, muitas vezes, `lazy` é mais lento do que uma versão não `lazy`. Ele deve ser usado somente quando ele realmente faz sentido, não apenas para evitar a construção de um `array` intermediário.

```ruby
require 'fruity'
r = 1..100
compare do
  lazy   { r.lazy.map(&:to_s).each_cons(2).map(&:join).to_a }
  direct { r     .map(&:to_s).each_cons(2).map(&:join)      }
end
# => direto é mais rápida do que por lazy 2x ± 0.1
```

###Tamanho Lazy

[Enumerator#size][16] pode ser chamado para obter o tamanho do enumerador sem consumi-lo (se disponível).

```ruby
# Ruby 2.0:
(1..100).to_a.permutation(4).size # => 94109400
loop.size # => Float::INFINITY
(1..100).drop_while.size # => nil
```

Ao criar enumeradores, seja com `to_enum`, `Enumerator::New` ou `Enumerator::Lazy::New` é possível definir um tamanho muito:

```ruby
# Ruby 2.0:
fib = Enumerator.new(Float::INFINITY) do |y|
  a = b = 1
  loop do
    y << a
    a, b = b, b+a
  end
end

still_lazy = fib.lazy.take(1_000_000).drop(42)
still_lazy.size # => 1_000_000 - 42

class Enumerable
  def skip(every)
    unless block_given?
      return to_enum(:skip, every) { size && (size+every)/(every + 1) }
    end
    each_slice(every+1) do |first, *ignore|
      yield last
    end
  end
end

(1..10).skip(3).to_a # => [1, 5, 9]
(1..10).skip(3).size # => 3, sem executar o loop
```

Detalhes adicionais e exemplos na doc de [to_enum][17].

###__dir__

Embora [require_relative][18] torna o uso de `File.dirname(__FILE__)` muito menos freqüentes, agora podemos usar [__dir__][19]

```ruby
# Ruby 1.8:
require File.dirname(__FILE__) + "/lib"
File.read(File.dirname(__FILE__) + "/.Gemfile")

# Ruby 1.9:
require_relative 'lib'
File.read(File.dirname(__FILE__) + '/.config')

# Ruby 2.0
require_relative 'lib' # há necessidade de usar __dir__ por isso!
File.read(__dir__ + '/.config')
```

###bsearch

Pesquisa binária já está disponível, usando [Array#bsearch][20] ou [Range#bsearch][21]:

```ruby
# Ruby 2.0:
ary = [0, 4, 7, 10, 12]
ary.bsearch {|x| x >=   6 } #=> 7
ary.bsearch {|x| x >= 100 } #=> nil

# Também em range, incluindo range de floats:
(Math::PI * 6 .. Math::PI * 6.5).bsearch{|f| Math.cos(f) <= 0.5}
# => Math::PI * (6+1/3.0)
```

###to_h

Existe agora uma forma oficial para converter uma classe a um Hash, utilizando `to_h`:

```ruby
# Ruby 2.0:
Car = Struct.new(:make, :model, :year) do
  def build
    #...
  end
end
car = Car.new('Toyota', 'Prius', 2014)
car.to_h # => {:make=>"Toyota", :model=>"Prius", :year=>2014}
nil.to_h # => {}
```

Isso foi implementado para `nil`, `Struct` e `OpenStruct`, mas não para `Array`/`Enumerable`:

```ruby
{hello: 'world'}.map{|k, v| [k.to_s, v.upcase]}
                .to_h # => NoMethodError:
# undefined method `to_h' for [["hello", "WORLD"]]:Array
```

Se você acha que isso seria um recurso útil, você deve [tentar convencer Matz][22].

###caller_locations

É usado para ser difícil saber qual o método chamado apenas. Que não foi muito eficiente, dado que o backtrace todo teve de ser retornado. Cada frames foi uma seqüência que precisava ser computado primeiramento pelo Ruby e provavelmente analisado depois.

Entra [caller_locations][23] que retorna a informação de uma forma de objeto e com uma api melhor que pode limitar o número de frames solicitados.

```ruby
# Ruby 1.9:
def whoze_there_using_caller
  caller[0][/`([^']*)'/, 1]
end

# Ruby 2.0:
def whoze_there_using_locations
  caller_locations(1,1)[0].label
end
```

Quanto mais rápido é? [Um teste simples][24] me dá um aumento de velocidade de 45x de um stacktrace curto, e 100x para um stacktrace de 100 entradas!

A informação extra, como o caminho do arquivo, número da linha, ainda são acessíveis, em vez de pedir para o `label`, para pedir `path` ou `lineno`.

###Otimizações

É difícil mostrar a maioria das otimizações de código, mas algumas otimizações agradáveis que foi feito no [Ruby 2.0.0][3]. Em particular, o GC foi otimizado, em particular para fazer bifurcar muito mais rápido.

Uma otimização que podemos demonstrar é fazer de imediatos muitos floats em sistemas de 64 bits. Isso evita a criação de novos objetos em muitos casos:

```ruby
# Ruby 1.9
4.2.object_id == 4.2.object_id # => false

# Ruby 2.0
warn "Optimization only on 64 bit systems" unless 42.size * 8 == 64
4.2.object_id == 4.2.object_id # => true (4.2 é imediato)
4.2e100.object_id == 4.2e100.object_id # => false (4.2e100 não é)
```

##O que mais?

Uma extensa lista de mudanças é o [arquivo NEWS][25].

##Eu quero!

Experimente hoje:

* Instalar com rvm: `rvm get head && rvm install 2.0.0` (note que `rvm get stable` não é suficiente!)
* Instalar com rbenv: `rbenv install 2.0.0-p0` (eu acho)
* Outra instalação: Veja as instruções de [ruby-lang.org][26]

Para aqueles que não podem atualizar ainda, você ainda pode ter um pouco da diversão com a minha gem [backports][27]. Ele faz `bsearch`, `lazy` e mais um par disponível para qualquer versão do Ruby. A lista completa está no [readme][28].

Aproveite o [Ruby 2.0.0][3]!

- - -

Até a proxima amigos...

E muito obrigado [Marc][1]...

And thank you very much [Marc][1]...

:D


[1]:https://twitter.com/malafortune
[2]:http://blog.marc-andre.ca/
[3]:http://www.ruby-lang.org/en/news/2013/02/24/ruby-2-0-0-p0-is-released/
[4]:http://blog.marc-andre.ca/2013/02/23/ruby-2-by-example/
[5]:https://twitter.com/yukihiro_matz/statuses/305334327938519040
[6]:https://twitter.com/yukihiro_matz
[7]:https://bugs.ruby-lang.org/issues/7701
[8]:http://stackoverflow.com/questions/13250447/can-i-have-required-named-parameters-in-ruby-2-x/15078852#15078852
[9]:http://bugs.ruby-lang.org/issues/7922
[10]:http://ruby-doc.org/core-2.0/String.html#method-i-prepend
[11]:http://ruby-doc.org/core-2.0/Module.html#method-i-refine
[12]:http://bugs.ruby-lang.org/projects/ruby-trunk/wiki/RefinementsSpec
[13]:http://benhoskin.gs/2013/02/24/ruby-2-0-by-example#refinements
[14]:http://ruby-doc.org/core-2.0/Enumerable.html
[15]:http://ruby-doc.org/core-2.0/Enumerable.html#method-i-lazy
[16]:http://ruby-doc.org/core-2.0/Enumerator.html#method-i-size
[17]:http://ruby-doc.org/core-2.0/Object.html#method-i-to_enum
[18]:http://ruby-doc.org/core-2.0/Kernel.html#method-i-require_relative
[19]:http://ruby-doc.org/core-2.0/Kernel.html#method-i-__dir__
[20]:http://ruby-doc.org/core-2.0/Array.html#method-i-bsearch
[21]:http://ruby-doc.org/core-2.0/Range.html#method-i-bsearch
[22]:http://bugs.ruby-lang.org/issues/7292
[23]:http://ruby-doc.org/core-2.0/Kernel.html#method-i-caller_locations
[24]:https://gist.github.com/marcandre/5041813
[25]:https://github.com/marcandre/ruby/blob/news/NEWS.rdoc
[26]:http://www.ruby-lang.org/en/downloads/
[27]:https://github.com/marcandre/backports
[28]:https://github.com/marcandre/backports#ruby-200