---
layout: post
title: "Reflexão e Metaprogramação - Variáveis e Constantes - #Ruby 1.9 - Part III"
date: 2012-08-29 23:34
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

<p>Hoje vamos continuar falando de <a href="http://www.ruby-doc.org/core-1.9.2/">Ruby</a>, é hora de continuar nos aprofundando um pouco mais de
<b>Reflexão e Metaprogramação</b> agora <b>Variáveis e constantes</b>... Estranho para alguns, mas, veremos que é simples!</p>

<h1>Variáveis e Constantes</h1>

`Kernel`, `Object`, `Module` definem métodos reflexivos para listar os nomes (como `strings`) de todas as variáveis definidas globais, atualmente
variáveis locais definidas, todas as variáveis de instância de um objeto, todas as variáveis de classe de uma classe ou módulo, e todas as constantes
de uma classe ou módulo:

``` ruby Variaveis e Constantes
global_variables   # => ["$DEBUG", "$SAFE", ...]
x = 1              # Define uma variável local
local_variables    # => ["x"]

# Define uma classe simples
class Point
  def initialize(x,y); @x,@y = x,y; end # define variáveis de instância
  @@classvar = 1                        # Define uma variável de classe
  ORIGIN = Point.new(0,0)               # Define uma constante
end

Point::ORIGIN.instance_variables # => ["@y", "@x"]
Point.class_variables            # => ["@@classvar"]
Point.constants                  # => ["ORIGIN"]
```

Os `global_variables`, `local_variables`, `instance_variables`, `class_variables` e métodos constantes retornam `arrays` de `strings` em Ruby 1.8 e
`arrays` de símbolos no Ruby 1.9.

<!--more-->
<h3>Consultando, Configuração e testando Variáveis</h3>

Além das variáveis listadas definidos e constantes, `object` e `Module` em Ruby também definem métodos de reflexão para consultas, a criação e
remoção de variáveis de instância, variáveis de classe e constantes. Não há métodos de propósito específico para consultar ou definir as variáveis 
locais ou variáveis globais, mas você pode usar o método `eval` para esta finalidade:

``` ruby Configurando Variavel
x = 1
varname = "x"
eval(varname)           # => 1
eval("varname = '$g'")  # Ajuste o "$g"
eval("#{varname} = x")  # Seta $g a 1
eval(varname)           
```

Note que `eval` avalia o seu código em um âmbito temporário. `eval` pode alterar o valor das variáveis locais que já existem. Mas qualquer nova 
variável local definida pelo código avaliado, são locais para a invocação do `eval` e deixam de existir quando ele retorna. (É como se o código
avaliado é executado no corpo de um bloco de variáveis-locais para um bloco não existem fora do bloco.)

Você pode consultar, definir e testar a existência de variáveis de instância em qualquer objeto e de variáveis de classe e constantes em qualquer
classe ou módulo:

``` ruby Consultar Variavel
o = Object.new
o.instance_variable_set(:@x, 0)   # Nota exigido o prefixo @
o.instance_variable_get(:@x)      # => 0
o.instance_variable_defined?(:@x) # => true

Object.class_variable_set(:@@x, 1)   # Privado no Ruby 1.8
Object.class_variable_get(:@@x)      # Privado no Ruby 1.8
Object.class_variable_defined?(:@@x) # => true; Ruby 1.9 e versões mais novas

Math.const_set(:EPI, Math::E*Math::PI)
Math.const_get(:EPI)             # => 8,53973422267357
Math.const_defined? :EPI         # => true
```

No Ruby 1.9, você pode passar `false` como o segundo argumento para `const_get` e `const_defined?` para especificar que esses métodos devem olhar
apenas para a classe atual ou módulo e não deve considerar constantes herdadas.

Os métodos para consultar e configurar as variáveis de classe são privadas no Ruby 1.8. Nessa versão, você pode invocá-las com `class_eval`:

``` ruby class_eval
String.class_eval { class_variable_set(:@@x, 1) }  # Seta @@x em String
String.class_eval { class_variable_get(:@@x) }     # => 1
```

`Object` e `Module` definem métodos privados para variáveis de instância indefinidas, variáveis de classe e constantes. Todos eles retornam o valor
da variável ou constante removida. Como esses métodos são privados, não pode invocá-los diretamente em um objeto, classe ou módulo, e você deve usar
um método eval ou o método de envio:

``` ruby instance_eval
o.instance_eval { remove_instance_variable :@x }
String.class_eval { remove_class_variable(:@@x) }
Math.send :remove_const, :EPI  # Usa send para chamar o método privado
```

O método `const_missing` de um módulo é invocado, se houver um, quando uma referência é feita a uma constante indefinida. Você pode definir este 
método para retornar o valor da constante chamada. (Esta característica pode ser usada, por exemplo, para implementar uma facilidade autoload em que
as classes ou módulos são carregados sob demanda). Aqui está um exemplo mais simples:

``` ruby cons_missing
def Symbol.const_missing(name)
  name # Retorna o nome da constante como um símbolo
end
Symbol::Test   # =>: Teste: constante indefinida avalia um símbolo
```

É isso ai amigos... Bora continuar estudando!

Em breve, irei postar um pouco sobre Engenharia! :)