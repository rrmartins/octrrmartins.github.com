---
layout: post
title: "Reflexão e Metaprogramação - Metodos - #Ruby 1.9 - Part IV"
date: 2012-08-31 20:12
comments: true
categories:
- Ruby API
- Integer
- String
- Object
- Kernel
- Ruby
- each
- Ruby 1.9
- The Ruby Programming Language
---
<!--more-->
<p>Hoje vamos continuar falando de <a href="http://www.ruby-doc.org/core-1.9.2/">Ruby</a>, é hora de continuar nos aprofundando um pouco mais de
<b>Reflexão e Metaprogramação</b> agora <b>Métodos</b>... Estranho para alguns, mas, veremos que é simples!</p>

<h1>Métodos</h1>

As classes de `Object` e `Module` de definir uma série de métodos para a listagem, de consulta, invocanção, e definição de métodos. Vamos considerar
cada categoria, por sua vez.

<h3>Listagem e Teste para Métodos</h3>

`Object` define métodos para listar os nomes dos métodos definidos no objeto. Estes métodos retornam `arrays` de nomes de métodos. Aqueles nomes são
`strings` em `Ruby 1.8` e `symbols` no `Ruby 1.9`:

``` ruby Listagem de Metodos
o = "a string"
o.methods                # => [nomes de todos os métodos públicos]
o.public_methods         # => é a mesma coisa
o.public_methods(false)  # Excluir métodos herdados
o.protected_methods      # => []: não há qualquer
o.private_methods        # => array de todos os métodos privados
o.private_methods(false) # Excluir herdados métodos privados
def o.single; 1; end     # Definir um método singleton
o.singleton_methods      # => ["single"] (ou: em 1,9 [único])
```

Também é possível consultar uma classe para os métodos que ele define em vez de consultar uma instância da classe. A seguir os métodos são definidos
por módulo. Como os métodos de objeto, eles retornam `arrays` de `strings` em `Ruby 1.8` e `arrays` de `symbols` em `Ruby 1.9`:

``` ruby Consultando uma Class
String.instance_methods == "s".public_methods                # => true
String.instance_methods(false) == "s".public_methods(false)  # => true
String.public_instance_methods == String.instance_methods    # => true
String.protected_instance_methods       										 # => []
String.private_instance_methods(false)  										 # => [:initialize, :initialize_copy]
```

Lembre-se que os métodos de classe de uma `class` ou `module` são métodos `singleton` do objeto de `class` ou `module`. Então, para listar métodos de
classe, usa `Object.singleton_methods`:

``` ruby Singleton
Math.singleton_methods # => [:atan2, :cos, :sin, :tan, :acos, :asin, :atan, :cosh, :sinh,
														 :tanh, :acosh, :asinh, :atanh, :exp, :log, :log2, :log10, :sqrt,
														 :cbrt, :frexp, :ldexp, :hypot, :erf, :erfc, :gamma, :lgamma]
```

Para além destes métodos de listagem, a classe `Module` define alguns predicados para testar se uma classe especificada ou módulo define um método de
instância nomeada:

``` ruby Testando metodo de classe
String.public_method_defined? :reverse     # => true
String.protected_method_defined? :reverse  # => false
String.private_method_defined? :initialize # => true
String.method_defined? :upcase!            # => true
```

`Module.method_defined?` verifica se o método chamado é definido como um método público ou protegido. Ele serve, essencialmente, com mesma finalidade
que `Object.respond_to?`. No `Ruby 1.9`, você pode passar `false` como o segundo argumento para especificar que os métodos herdados não deve ser
considerados.


<h3>Obtenção de método de objetos</h3>

Para consultar um método específico nomeado, chamar o método em qualquer objeto ou `instance_method` em qualquer módulo. O primeiro retorna um
objeto do Método exigído pelo receptor, e o último retorna um `UnboundMethod`. No `Ruby 1.9`, você pode limitar sua pesquisa aos métodos públicos
métodos chamando `public_method` e `public_instance_method`. Nós cobrimos esses métodos e os objetos que eles voltam no Método do Objeto:

``` ruby
"s".method(:reverse)             # => objeto Método
String.instance_method(:reverse) # => objeto UnboundMethod
```

<h3>Invocando Métodos</h3>

Como observado anteriormente, e em métodos de objetos, você pode usar o método `method` de qualquer objeto para obter um objeto que representa
`Method` de objeto. Objetos `Method` têm um método `call` assim como objetos `Proc` fazem, você pode usá-lo para chamar o método.

Normalmente, é mais simples para invocar um método chamado de um objeto especificado com `send`:

``` ruby Send
"hello".send :upcase        # => "hello": invocar um método de instância
Math.send(:sin, Math::PI/2) # => 1.0: invocar um método de classe
```

`send` invoca em seu receptor o método nomeado pelo seu primeiro argumento, passando quaisquer argumentos restantes a esse método. O nome `send`
deriva da linguagem orientada a objetos em que invocando um método é chamado, enviando uma mensagem, a um objeto.

`send` pode invocar qualquer método chamando um objeto, incluindo métodos privados e protegidos. Vimos `send` utilizado anteriormente para invocar o
método privado `remove_const` de um objeto `Module`. Porque funções globais são realmente métodos privados de `Object`, podemos usar `send` para
chamar esses métodos em qualquer objeto (embora isso não é algo que nós, nunca realmente queremos fazer):

``` ruby Send
"hello".send :puts, "world"         # imprime "world"
```

Ruby 1.9 define `public_send` como uma alternativa para `send`. Este método funciona como `send`, mas só irá chamar os métodos públicos, não
métodos privados ou protegidos:

``` ruby public_send_
"hello".public_send :puts, "world"  # raises NoMethodError
```

`send` é um método fundamental do objeto, mas tem um nome comum que pode ser substituído em subclasses. Portanto, Ruby
define `__send__` como um sinônimo, e emite um aviso se você tentar excluir ou redefinir `__send__`.


<h3>Definindo, Indefinindo e Métodos Alias</h3>

Se você quiser definir um novo método de instância de uma classe ou módulo, utilize `define_method`. Este método de instância de `Module` leva o
nome do novo método (como um `symbol`), como seu primeiro argumento. O corpo do método é fornecido seja por um método importante que entendemos o
`define_method` que é privado. Você deve estar dentro da classe ou módulo que pretende usá-lo para chamá-lo:

``` ruby define_method
# Adicione um método de instância chamado m para a Classe C com o corpo b
def add_method(c, m, &b)
  c.class_eval {
    define_method(m, &b)
  }
end

add_method(String, :greet) { "Hello, " + self }

"world".greet   # => "Hello, world"
```

Para definir um método de classe (ou qualquer método `singleton`) com `define_method`, invocá-lo no `eigenclass`:

``` ruby eigenclass
def add_class_method(c, m, &b)
  eigenclass = class << c; self; end
  eigenclass.class_eval {
    define_method(m, &b)
  }
end

add_class_method(String, :greet) {|name| "Hello, " + name }

String.greet("world")  # => "Hello, world"
```

No Ruby 1.9, você pode usar mais facilmente `define_singleton_method`, que é um método de objeto:

``` ruby define_singleton_method
String.define_singleton_method(:greet) {|name| "Hello, " + name }
```

Uma deficiência do `define_method` é que ele não permite que você especifique um corpo de método que espera um bloco. Se você precisar dinamicamente
criar um método que aceita um bloco, você vai precisar usar a instrução `def` com `class_eval`. E se o método que está criando é suficientemente
dinâmico, você pode não ser capaz de passar um bloco para o `class_eval` e em vez disso tem de especificar a definição do método como uma seqüência
a ser avaliada.

Para criar um sinônimo ou um `alias` para um método existente, normalmente você pode usar a declaração `alias`:

``` ruby alias
alias "plus" +         # "plus" é um sinônimo para o operador +
```
Ao programar dinamicamente, no entanto, às vezes você precisa usar `alias_method`. Como `define_method`, `alias_method` é um método particular do
`Module`. Como método, ele pode aceitar duas expressões arbitrárias como seus argumentos, em vez de exigir dois identificadores para ser codificado
em seu código fonte. Como método, também requer uma vírgula entre seus argumentos. É `alias_method` muitas vezes utilizados para métodos de
encadeamento de alias existentes. Aqui está um exemplo simples:

``` ruby alias_method
# Crie um alias para o método m da classe (ou módulo) c
def backup(c, m, prefix="original")
  n = :"#{prefix}_#{m}"    # Calcule o alias
  c.class_eval {           # Porque alias_method é privado
    alias_method n, m      # Fazer n um alias para m
  }
end

backup(String, :reverse)
"test".original_reverse # => "tset"
```

Você pode usar a declaração `undef` para indefinir um método. Isso só funciona se você pode expressar o nome de um método como um identificador
codificado no programa. Se você precisar excluir dinamicamente um método cujo nome tem sido calculado pelo seu programa, você tem duas opções: ou
`remove_method` ou `undef_method`. Ambos são métodos privados de Módulo. `remove_method` remove a definição do método a partir da classe corrente.
Se existe uma versão definida por uma superclasse, que a versão vai agora ser herdada. `undef_method` é mais grave, que impede qualquer invocação do
método especificado por meio de uma instância da classe, mesmo se houver uma versão herdada do método.

Se você definir uma classe e quer evitar alterações dinâmicas para ele, simplesmente chame o método `freeze` da classe. Uma vez "congelado", a classe
não pode ser alterada.


<h3>Manipulação de métodos indefinidos</h3>

Quando o método do algoritmo de resolução de nomes não consegue encontrar um método, ele procura um método chamado `method_missing` em vez disso.
Quando este método é chamado, o primeiro argumento é um `simbol` que dá nome ao método que não pôde ser encontrado. Este `simbol` é seguido por todos
os argumentos que deveriam ser passados para o método original. Se houver um bloqueio associado com o método de invocação, que também é passado para
 o bloco `method_missing`.

A implementação padrão de `method_missing`, no módulo de `Kernel`, simplesmente levanta um `NoMethodError`. Essa exceção, se não detectada, faz com
que o programa possa sair com uma mensagem de erro, que é o que você normalmente espera que aconteça quando você tenta invocar um que método não
existe.

Definindo seu próprio método `method_missing` para uma classe que permite a você uma oportunidade de lidar com qualquer tipo de chamada em casos da
classe. O gancho `method_missing` é um dos mais poderosos do Ruby com capacidades dinâmicas, e um dos mais vulgarmente utilizado nas técnicas
de metaprogramação. O código de exemplo a seguir adiciona um método `method_missing` à classe `Hash`. Ela nos permite consultar ou definir o valor de
qualquer chave chamada como se a chave fosse o nome de um método:

``` ruby Hash Class
class Hash
  # Permiti valores de hash para ser consultado e definido como se fossem atributos.
  # Simulamos getters e setters para atributos de qualquer tecla.
  def method_missing(key, *args)
    text = key.to_s

    if text[-1,1] == "="               # Se termina com chave = definir um valor
      self[text.chop.to_sym] = args[0] # Faixa de chave
    else # Caso contrário ...
      self[key]                        # ... apenas retornar o valor da chave
    end
  end
end

h = {} 				 # Criar um objeto vazio de hash
h.one = 1 		 # O mesmo que h[:one] = 1
puts h.one     # Imprime 1. Mesmo que põe h[:one]
```

<h3>Definir Visibilidade do Método</h3>

Visibilidades de método: `Public`, `Protected`, `Private` introduzindo o `public`, `protected`, e `private`. Parecem palavras-chave da linguagem, mas
são na verdade métodos de instância privados definidos pelo módulo. Estes métodos são geralmente utilizados como uma parte estática da definição de
uma classe. Mas, com `class_eval`, eles também podem ser usados dinamicamente:

``` ruby class_eval com Visibilidade de Metodo
String.class_eval { private :reverse }
"hello".reverse  # NoMethodError: private method 'reverse'
```

`private_class_method`` e `public_class_method` são semelhantes, exceto que eles operam em métodos de classe e são eles métodos públicos:

``` ruby Matematica
# Fazer todos os métodos de Matemática privados
# Agora temos que incluem matemática, a fim de chamar seus métodos
Math.private_class_method *Math.singleton_methods
```

É isso ai amigos, um post, para introduzir o #StartupDevRumble .. =D

Até o proximo!