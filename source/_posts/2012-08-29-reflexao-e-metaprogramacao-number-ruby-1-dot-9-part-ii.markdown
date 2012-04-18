---
layout: post
title: "Reflexão e Metaprogramação #Ruby 1.9 - Part II"
date: 2012-08-29 22:15
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

<p>Hoje vamos continuar falando de <a href="http://www.ruby-doc.org/core-1.9.2/">Ruby</a>, é hora de continuar nos aprofundando um pouco mais de
<b>Reflexão e Metaprogramação</b> agora <b>Avaliando Strings e Blocos</b>... Estranho para alguns, mas, veremos que é simples!</p>

<h1>Avaliando Strings e Blocos</h1>

Um dos mais poderosos reflexivos e de características direta do Ruby, é seu método `eval`. Se o seu programa Ruby pode gerar uma seqüência de código
válido Ruby, o método `Kernel.eval` pode avaliar que o código:

``` ruby Kernel.eval
x = 1
eval "x + 1"  # => 2
```

`eval` é uma função poderosa, mas ao menos que você realmente está escrevendo um programa `shell` (como irb) que executa as linhas de códigos Ruby
inserido por um usuário é improvável que você realmente precisa. (E em um contexto de rede, quase nunca é seguro para chamar `eval` no texto recebido
de um usuário, que podia conter um código malicioso). Programadores inexperientes, por vezes, acabam usando eval como uma muleta. Se você tiver que  
usá-lo em seu código, ver se não há uma maneira de evitá-lo. Dito isto, há algumas maneiras mais úteis para usar métodos `eval` e `eval-like`.
<!--more-->

<h3>Associações e eval</h3>

Um objeto de Associação, representa o estado de variáveis `bindings` de Ruby em algum momento. O objeto `Kernel.binding` retorna as ligações em vigor
no local da chamada. Você pode passar um objeto de associação como o segundo argumento para `eval`, e a `String` especificada será avaliada no 
contexto dessas ligações. Se, por exemplo, definir um método de instância que retorna um objeto `Binding` que representa a variável de ligações
dentro de um objeto, então nós podemos usar essas ligações para consultar e definir as variáveis de instância do objeto. Podemos conseguir isso como segue:

``` ruby Binding
class Object # Abre objeto para adicionar um novo método
  def bindings # Nota plural sobre este método
    binding # Este é o método de Kernel predefinido
  end
end

class Test # Uma classe simples com uma variável de instância
  def initialize(s); @x = s; end
end

t = Test.new(10) # Criar um objeto test
eval("@x", t.bindings)  # => 10: Espia dentro t
```

Note que não é realmente necessário definir um método de `Object.bindings` deste tipo para observar nas variáveis de instância de um objeto. Vários
outros métodos descritos logo oferecem maneiras mais fáceis de consulta (e de conjunto) do valor das variáveis de instância de um objeto.

Os objetos `Proc` definem um método público de ligação que retorna uma Associação de objeto que representa a variável ligação em vigor para o corpo
do `Proc`. Além disso, o método `eval` permite que você passe um objeto `Proc` em vez de um objeto de associação como o segundo argumento.

Ruby 1.9 define um método `eval` em objetos de associação, de modo em vez de passar uma associação como o segundo argumento para o `eval` global, 
você pode, em vez de invocar o método `eval` em uma ligação. Qual você escolhe é puramente uma questão de estética, as duas técnicas são equivalentes.


<h3>instance_eval e class_eval</h3>

A classe `Object` define um método chamado `instance_eval`, e a classe `Module` define um método chamado `class_eval`. `module_eval` é um sinônimo
para `class_eval`. Ambos os métodos avaliam código Ruby, como o `eval` faz, mas existem duas diferenças importantes. A primeira diferença é a que eles
avaliam o código no contexto do objecto específico ou no contexto especificado de cada módulo do objecto ou é o valor enquanto o código está sendo 
avaliado. Aqui estão alguns exemplos:

``` ruby instance_eval e class_eval
o.instance_eval("@x")  # Retorna o valor da variável de instância o @x

# Define um len método de instância de String para retornar comprimento da String
String.class_eval("def len; size; end")

# Aqui está outra maneira de fazer isso
# O código citado se comporta como se fosse dentro da "class String" e "end"
String.class_eval("alias len size")

# Use instance_eval para definir método de classe String.Empty
# Note que as aspas dentro da aspas fica um pouco complicado ...
String.instance_eval ("def vazio;''; fim")
```

Observe a diferença sutil, mas crucial entre `instance_eval` e `class_eval` quando o código que está sendo avaliado contém uma definição de método.
`instance_eval` define métodos únicos do objeto (e isso resulta em métodos de classe quando for chamado em uma classe de objeto). `class_eval` define métodos de instância regulare.

A segunda diferença importante entre estes dois métodos e o eval global é que `instance_eval` e `class_eval` pode aceitar um bloco de código para 
avaliar. Quando passa um bloco em vez de uma `String`, o código do bloco é executado no contexto apropriado. Aqui, por conseguinte, são alternativas
para as invocações anteriorment mostrada:

``` ruby instance_eval e class_eval
o.instance_eval { @x }
String.class_eval {
  def len
    size
  end
}
String.class_eval { alias len size }
String.instance_eval { def empty; ""; end }
```

<h3>instance_exec e class_exec</h3>

Ruby 1.9 define dois métodos de avaliação a mais: `instance_exec` e `class_exec` (e seu alias, `module_exec`). Estes métodos de avaliação de um bloco 
(Mas não uma cadeia) de código, no contexto do objeto receptor, como fazem `instance_eval` e `class_eval`. A diferença é que o métodos `exec` aceita
argumentos e passá para o bloco. Assim, o bloco do código é avaliado no contexto de um objeto determinado, com parâmetros cujos valores vêm a partir
do exterior do objecto.

Até o proximo post amigos! :P