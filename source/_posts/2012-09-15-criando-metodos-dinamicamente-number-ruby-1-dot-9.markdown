---
layout: post
title: "Criando Métodos Dinamicamente - #Ruby 1.9"
date: 2012-09-15 20:38
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
<b>Reflexão e Metaprogramação</b> agora <b>Criando Métodos Dinamicamente</b>... Estranho para alguns, mas, veremos que é simples!</p>

<h1>Criando Métodos Dinamicamente</h1>

Uma técnica importante em metaprogramação é a utilização de métodos que criam métodos. Os métodos `attr_reader` e `attr_accessor` são exemplos. Estes
métodos de instância privados do `Module` são usados como palavras-chave dentro de definições de classe. Eles aceitam nomes de atributos como seus
argumentos, e dinamicamente criam métodos com esses nomes. Os exemplos que se seguem são variantes sobre essa criação de atributos assessores dos
métodos e demonstra duas formas diferentes de criar dinamicamente métodos como este.
<!--more-->

<h3>Métodos definidos com class_eval</h3>

O Exemplo 1-1 define métodos privados de instância do `Module` chamado `readonly` e `readwrite`. Estes métodos funcionam como `attr_reader`
e `attr_accessor`, e eles estão aqui para demonstrar como esses métodos são implementados. A implementação é na verdade muito simples: `readonly` e
`readwrite` primeiro construi uma seqüência de código Ruby contendo as instruções necessárias para definir `def` os métodos de acesso apropriados. 
Em seguida, eles avaliam que a seqüência de código usando `class_eval`. Usando `class_eval` como esta gera a sobrecarga de analisar a cadeia de código. 
A vantagem, contudo, é que os métodos que definem não precisa usar as APIs reflexivas, pois eles podem consultar ou definir o valor de uma variável de
instância diretamente.

``` ruby Exemplo 1-1. Métodos de atributos com class_eval

class Module
  private # Os métodos que se seguem são todos privados

  # Este método funciona como attr_reader, mas tem um nome mais curto
  def readonly(*syms)
    return if syms.size == 0  # Se nenhum argumento, não faz nada
    code = ""                 # Comece com uma cadeia vazia de código
    # Gera uma seqüência de código Ruby para definir métodos leitores de atributos.
    # Observe como o símbolo é interpolado para a seqüência de código.
    syms.each do |s|                     # Para cada símbolo
      code << "def #{s}; @#{s}; end\n"   # O método de definição
    end
    # Finalmente, class_eval o código gerado para criar métodos de instância.
    class_eval code
  end

  # Este método funciona como attr_accessor, mas tem um nome mais curto.
  def readwrite(*syms)
    return if syms.size == 0
    code = ""
    syms.each do |s|
      code << "def #{s}; @#{s} end\n"
      code << "def #{s}=(value); @#{s} = value; end\n"
    end
    class_eval code
  end
end
```

<h3>Métodos definidos com define_method</h3>

O Exemplo 1-2 é uma posição diferente sobre os assessores de atributos. O método `attributes` é algo como o método `readwrite` definido no `Exemplo
1-1`. Em vez de tomar qualquer número de nomes de atributos como argumentos, que espera um único objeto `hash`. Este `hash` deve ter nomes de atributos
como suas chaves, e deve mapear os nomes de atributos para os valores padrões para os atributos. O método `class_attrs` funciona como atributos, mas
define os atributos de classe em vez de atributos de instância.

Lembre-se que Ruby permite que as chaves para ser omitidas em torno de `hash` literais quando eles são o argumento final em uma invocação de método.
Assim, o método `attributes` pode ser chamado com um código como este:

``` ruby Metodo attributes
class Point
  attributes :x => 0, :y => 0
end
```

No Ruby 1.9, podemos usar a sintaxe do `hash` é mais sucinta:

``` ruby Attibutes
class Point
  attributes x:0, y:0
end
```

Este é outro exemplo que utiliza sintaxe flexível de Ruby para criar métodos que se comportam como palavras-chave de linguagem.

A implementação do método de `attributes` no `Exemplo 1-2` é um pouco diferente do que a do método `readwrite` no `Exemplo 1-1`. Em vez de definir uma
seqüência de código Ruby e avaliá-lo com `class_eval`, o método `attributes` define o corpo dos acessos de atributos de um bloco e define os métodos 
que utilizam `define_method`. Uma vez que este método técnico de definição não nos permitem identificadores interpolares diretamente no corpo do
método, temos de confiar em métodos reflexivos, como `instance_variable_get`. Devido a isso, os assessores definidos com `attributes` são susceptíveis
de ser menos eficientes do que os definidos com `readwrite`.

Um ponto interessante sobre o método `attributes` é que não armazena explicitamente os valores padrões para os atributos em uma variável de classe de
qualquer tipo. Em vez disso, o valor por defeito para cada atributo é capturado pelo âmbito de bloquear o método usado para definir.

O método `class_attrs` define os atributos de classe muito simples: ele invoca `attributes` na <a href="http://blog.caelum.com.br/metaprogramacao-eigenclass-em-ruby/">eigenclass</a> da classe. Este
significa que os métodos resultantes usam variáveis de instância de classe em vez de variáveis de classe regular.

``` ruby Exemplo 1-2. Métodos de atributos com define_method

class Module
  # Este método define os atributos de métodos de reader e writer de nomeado
  # attributes, mas aguarda um argumento de attributes de nomes em hash mapeado para
  # Valores padrões. Os métodos de reader gerados atributos retorna o
  # Valor padrão se a variável de instância ainda não foi definido.
  def attributes(hash)
    hash.each_pair do |symbol, default|   # Para cada par de atributo/default
      getter = symbol                     # Nome do método getter
      setter = :"#{symbol}="              # nome do método setter
      variable = :"@#{symbol}"            # nome da variável de instância
      define_method getter do             # Definir o método getter
        if instance_variable_defined? variable
          instance_variable_get variable  # Retorna variável, se definido
        else
          default                         # Caso contrário retornar padrão
        end
      end

      define_method setter do |value|     # Defini método setter
        instance_variable_set variable,   # Defina a variável de instância
                              value       # Para o valor do argumento
      end
    end
  end

  # Este método funciona como atributos, mas define métodos de classe em vez de
  # Invocar atributos no eigenclass em vez de em si mesmo.
  # Note que os métodos definidos usam variáveis de instância de classe
  # Em vez de variáveis de classe regulares.
  def class_attrs(hash)
    eigenclass = class << self; self; end
    eigenclass.class_eval { attributes(hash) }
  end

  # Ambos os métodos são privados
  private :attributes, :class_attrs
end
```

É isso ai amigos, até o proximo post! 