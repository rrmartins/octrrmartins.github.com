---
layout: post
title: "Missing Methods e Missing Constants - #Ruby 1.9"
date: 2012-09-15 12:46
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
<b>Reflexão e Metaprogramação</b> agora <b>Missing Methods e Missing Constants</b>... Estranho para alguns, mas, veremos que é simples!</p>

<h1>Missing Methods e Missing Constants</h1>

O método `method_missing` é uma parte fundamental da pesquisa de algoritmo em método em Ruby e fornece uma maneira poderosa para capturar e manipular
invocações arbitrárias sobre um objeto. O método `const_missing` de `Module` executa uma função similar para o algoritmo de pesquisa constante e que
nos permite calcular ou lazily inicializa as constantes. Os exemplos que seguem demonstram ambos métodos.
<!--more-->

<h3>Constantes Unicode Codepoint com const_missing</h3>

O Exemplo 1-1 define um módulo `Unicode` que aparece para definir uma constante (uma string UTF-8) para cada `Unicode codepoint` de `U+0000` a 
`U+10FFFF`. O único modo prático para suportar estas muitas constantes é a utilização do método `const_missing`. O código faz a suposição de que se uma
constante é referenciada uma vez, é susceptível de ser utilizado de novo, de modo que o método `const_missing` chama `Module.const_set` para definir
uma constante real para se referir a cada valor calculado.

``` ruby Exemplo 1-1. Constantes Unicode codepoint com const_missing

# Todo codepoints Unicode. Ele usa const_missing para defini-los lazily.
# Exemplos:
#   copyright = Unicode::U00A9
#   euro = Unicode::U20AC
#   infinity = Unicode::U221E
module Unicode
  # Este método permite-nos definir constantes Unicode codepoint lazily.
  def self.const_missing(name)  # constante indefinida passada como um símbolo
    # Verifique se o nome da constante é da forma certa.
    # Capital U seguido de um número hexadecimal entre 0000 e 10FFFF.
    if name.to_s =~ /^U([0-9a-fA-F]{4,5}|10[0-9a-fA-F]{4})$/
      # $1 é o número hexadecimal correspondente. Converte em um inteiro.
      codepoint = $1.to_i(16)
      # Converte o número para uma string UTF-8 com a magia do Array.pack.
      utf8 = [codepoint].pack("U")
      # Faz a imutável string UTF-8.
      utf8.freeze
      # Define uma constante real para pesquisa mais rápida da próxima vez, e retorna
      # O texto UTF-8 para este tempo.
      const_set(name, utf8)
    else
      # Eleva um erro para constantes do formulário errado.
      raise NameError, "Uninitialized constant: Unicode::#{name}"
    end
  end
end
```

<h3>Rastreamento Invocações de método com method_missing</h3>

No início deste post, demonstrei uma extensão para a classe `Hash` usando `method_missing`. Agora, no `Exemplo 1-2`, temos que demonstrar o uso de
`method_missing` delega as chamadas arbitrárias em um objeto para outro objeto. Neste exemplo, o que fazemos nesta ordem para a saída de rastreamento
de mensagens para o objeto.

`Exemplo 1-2` define um método de instância `Object.trace` e uma classe `TracedObject`. O método `trace` retorna uma instância de `TracedObject` que
usa `method_missing` para pegar invocações, rastreá-las, e delegá-las ao objeto que está sendo rastreado. Você pode usar como este:

``` ruby Rastrear Metodos
a = [1,2,3]
a.reverse
puts a[2]
puts a.fetch(3)
```

Isso produz a seguinte saída de rastreamento:

``` ruby Retorno
Invocando: a.reverse()
Volta: [3, 2, 1] a partir de a.reverse
Invocando: a.fetch(3)
Raising: IndexError: índice de 3 de matriz de a.fetch
```

Note-se que, além de demonstrar `method_missing`, o `Exemplo 1-2` demonstra também `Module.instance_methods`, `Module.undef_method` e `Kernel.caller`.

``` ruby Exemplo 1-2. Rastreamento invocações de método com method_missing

# Se comporta exatamente como o original, mas que traça todas as chamadas de método
# No objeto. Se rastreamento mais de um objeto, especifique um nome para
# Aparecer na saída. Por padrão, as mensagens serão enviadas para STDERR,
# Mas você pode especificar qualquer stream (ou qualquer objeto que aceita strings
# Como argumentos para <<).
classe Object
  def trace(name="", stream=STDERR)
    # Retorna um TracedObject que traços e delegados tudo mais para nós.
    TracedObject.new(self, name, stream)
  end
end

# Esta classe usa method_missing para rastrear chamadas de método e
# Então delega ele para algum outro objeto. Ele exclui a maioria de seus próprios
# Métodos de instância para que eles não ficam no caminho de method_missing.
# Note que apenas métodos invocados através da TracedObject será rastreado.
# Se o objeto delegado chama métodos em si, aquelas invocações
# Não será rastreado.
class TracedObject
  # Indefine todos os nossos métodos de instância públicos não críticos.
  # Observe o uso do Module.instance_methods e Module.undef_method.
  instance_methods.each do |m|
    m = m.to_sym   # Ruby 1.8 retorna string, em vez de símbolos
    next if m == :object_id || m == :__id__ || m == :__send__
    undef_method m
  end

  # Inicializa esta instancia do TracedObject.
  def initialize(o, name, stream)
    @o = o            # objeto que delegar
    @n = name         # O nome do objeto a aparecer no rastreamento de mensagens
    @trace = stream   # Onde essas mensagens de rastreamento são enviados
  end

  # Este é o principal método de TracedObject. Ele é invocado por apenas
  # Sobre qualquer invocação de método em um TracedObject.
  def method_missing(*args, &block)
    m = args.shift         # O primeiro é o nome do método
    begin
      # Acompanhe a invocação do método.
      arglist = args.map {|a| a.inspect}.join(', ')
      @trace << "Invoking: #{@n}.#{m}(#{arglist}) at #{caller[0]}\n"
      # Invoque o método em nosso objeto de delegação e obtem o valor de retorno.
      r = @o.send m, *args, &block
      # Traça um retorno normal do método.
      @trace << "Returning: #{r.inspect} from #{@n}.#{m} to #{caller[0]}\n"
      # Retorna o valor que o objeto delegado retornado.
      r
    rescue Exception => e
      # Traçar um retorno anormal do método.
      @trace << "Raising: #{e.class}:#{e} from #{@n}.#{m}\n"
      # E re-envia qualquer exceção que o objeto delegado levantada.
      raise
    end
  end

  # Retorna o objeto que delegou.
  def __delegate
    @o
  end
end
```

<h3>Objetos sincronizados por delegação</h3>

No post anterior, vimos um método global sincronizado, que aceita um objeto e executa um bloco sob a proteção do `Mutex` associado a esse objeto. 
A maior parte do exemplo consistiu na aplicação do método `Object.mutex`. O método sincronizado foi trivial:

``` ruby Mutex
def synchronized(o)
  o.mutex.synchronize { yield }
end
```

O Exemplo 1-3 modifica este método de modo que, quando chamado sem um bloco, ele retorna um invólucro em torno do objeto `SynchronizedObject`.
`SynchronizedObject` é uma classe que delega com base em `method_missing`. É muito parecido com a classe `TracedObject`, Exemplo 1-2, mas Ruby 1.9 está
escrito com uma subclasse de `BasicObject`, por isso não há necessidade de excluir explicitamente os métodos de instância de objeto. Note que o código
deste exemplo não está sozinho, que exige o método `Object.mutex` definido anteriormente.

``` ruby Exemplo 1-3. Métodos de sincronização com method_missing

def synchronized(o)
  if block_given?
    o.mutex.synchronize { yield }
  else
    SynchronizedObject.new(o)
  end
end

# A classe delega usando method_missing de segurança da thread
# Em vez de estender objetos e excluir nossos métodos que acabamos de estender de
# BasicObject, que é definido no Ruby 1.9. BasicObject não
# Herda do Object ou do Kernel, de modo que os métodos de uma BasicObject não pode
# Chamar os métodos de nível superior: eles não são apenas lá.
class SynchronizedObject  < BasicObject
  def initialize(o); @delegate = o;  end
  def __delegate; @delegate; end

  def method_missing(*args, &block)
    @delegate.mutex.synchronize {
      @delegate.send *args, &block
    }
  end
end
```

É isso ai amigos... :)

Até o proximo! :D