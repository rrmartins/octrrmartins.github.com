---
layout: post
title: "Reflexão e Metaprogramação - Hooks - #Ruby 1.9 - Part V"
date: 2012-09-05 23:04
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

<p>Hoje vamos continuar falando de <a href="http://www.ruby-doc.org/core-1.9.2/">Ruby</a>, é hora de continuar nos aprofundando um pouco mais de
<b>Reflexão e Metaprogramação</b> agora <b>Hooks</b>... Estranho para alguns, mas, veremos que é simples!</p>

<h1>Hooks</h1>

`Class`, `Module`, e os vários métodos de retorno da chamada do `Objectimplement`, ou `hooks`. Estes métodos não são definidos por padrão, mas se você
defini-los para um módulo de classe, ou objeto, então eles vão ser invocados quando ocorrerem determinados eventos. Isso lhe dá uma oportunidade para
estender o comportamento de Ruby quando as classes são uma subclasse, quando os módulos estão incluídos, ou quando os métodos são definidos. Métodos de
`hook` (exceto para alguns mais preteridos não descritos aqui) têm nomes que terminam em "ed".
<!-- more -->
Quando uma nova classe é definida, Ruby chama o método de classe herdada na superclasse da nova classe, passando o objeto da nova classe como 
argumento. Isso permite adicionar classes para comportamento ou impor restrições sobre seus descendentes. Lembre-se que os método de classe são
herdados, de modo que a um método herdado será chamado se ele é definido por qualquer um dos antepassados da nova classe. Definir `Object.inherited`
para receber a notificação de todas as novas classes que são definidas:

``` ruby Object.inherited
def Object.inherited(c)
  puts "class #{c} < #{self}"
end
```

Quando um módulo é incluído numa classe ou em outro módulo, o método da classe incluída do módulo incluído é chamado com o objeto de classe ou módulo
em que foi incluído como um argumento. Isto dá o módulo incluído uma oportunidade para aumentar ou alterar a classe da maneira que ele efetivamente 
quer ser permitido que um módulo define o seu próprio significado para incluir. Além da adição de métodos para a classe em que se insere, um módulo com
um método incluído também que pode alterar os métodos existentes dessa classe, por exemplo:

``` ruby 
module Final             # Uma classe que inclui Final não pode ser uma subclasse
  def self.included(c)   # Quando incluídos na classe C
    c.instance_eval do   # Defini um método de classe de c
      def inherited(sub) # Para detectar subclasses
        raise Exception, # E abortar com uma exceção
              "Tentativa de criar subclasse #{sub} da classe final #{self}"
      end
    end
  end
end
```

Da mesma forma, se um módulo de classe define um método chamado `extended`, este método será invocado em qualquer momento que o módulo é utilizado para
estender um objeto (com `Object.extend`). O argumento para o método de extensão será o objeto que foi estendido, é claro, e do método estendido pode
tomar quaisquer ações que quer o objeto.

Além de `hooks` para rastreiar as classes e os módulos que incluem, também existem `hooks` para rastrear os métodos de classes e módulos e os únicos 
métodos de objetos arbitrários. Defini um método de classe chamado `method_added` para qualquer classe ou módulo e ele será chamado quando um método de
instância é definido para essa classe ou módulo:

``` ruby method_added
def String.method_added(name) 
  puts "New instance method #{name} added to String"
end
```

Note-se que o método da classe `method_added` é herdado pelas subclasses da classe em que ele está definido. Mas nenhum argumento de classe é passado
para o `hook`, pelo que não há maneira de dizer se o método chamado foi adicionado à classe que define `method_added` ou se ela foi adicionada a uma
subclasse desta classe. A solução para este problema é definir um `hook` que herda em qualquer classe uma definição de um `hook` `method_added`. 
O método pode então ser herdado um método que defini `method_added` para cada subclasse.

Quando um método `singleton` é definido para qualquer objeto, o método `singleton_method_added` é invocado sobre o objeto, passando o nome do novo
método. Lembre-se que para as classes, métodos `singleton` são métodos de classe:

``` ruby singleton_method_added
def String.singleton_method_added(name)
  puts "New class method #{name} added to String"
end
```

Curiosamente, Ruby invoca esse `hook` `singleton_method_added` quando o método de `hook` em si é definido previamente. Aqui é um outro uso do `hook`.
Neste caso, `singleton_method_added` é definido como um método de instância de uma classe que inclui um módulo. É notificado de qualquer métodos
`singleton` adicionados a instâncias da classe:

``` ruby singleton_method_added
# Incluindo este módulo em uma classe impede que instâncias da classe
# De ter métodos singleton adicionados a eles. Quaisquer métodos singleton acrescentados
# São imediatamente removidos novamente.
module Strict
  def singleton_method_added(name)
    STDERR.puts "Warning: singleton #{name} added to a Strict object"
    eigenclass = class << self; self; end
    eigenclass.class_eval { remove_method name }
  end
end
```

Além de `method_added` e `singleton_method_added`, há `hook` para rastreamento quando os métodos de instância e métodos `singleton` são removidos ou
indefinidos. Quando um método de instância é removido ou indefinido em uma classe ou módulo, os métodos de classe `method_removed` e `method_undefined`
são invocados nesse módulo. Quando um método `singleton` é removido ou indefinido em um objeto, os métodos `singleton_method_removed` e 
`singleton_method_undefined` são invocados nesse objeto.

Por fim, note que os métodos `method_missing` e `const_missing` também se comportam como métodos de `hook`.

Até o proximos amigos!