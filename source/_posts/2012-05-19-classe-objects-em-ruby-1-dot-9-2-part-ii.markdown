---
layout: post
title: "Classe Objects em Ruby 1.9.2 - Part II"
date: 2012-05-19 13:14
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

<h3>Identidade do objeto</h3>

Cada objeto tem um identificador de objeto, um Fixnum, que você pode obter com o método object_id. O valor retornado por este
método é constante e exclusivo para a vida útil do objeto. Enquanto o objeto é acessível, ele terá sempre a mesma identificação, e não
outro objeto que irá partilhar do mesmo ID.

O ID de método é um sinônimo preterido para object_id. Ruby 1.8 emite um aviso se você usá-lo, e ele foi removido em
Ruby 1.9.

__id__ é um sinônimo válido para object_id. Ele existe como um retorno, assim você pode acessar ID de um objeto, mesmo se o método
object_id foi indefinido ou substituído.

A classe <a href="http://ruby-doc.org/core-1.9.3/Object.html">Object</a> implementa o método de hash para simplesmente retornar um
ID objeto.

<h3>Classe de Objeto e Tipo de Objeto</h3>

Existem várias maneiras de determinar a classe de um objeto em Ruby. O mais simples é simplesmente perguntar para ele:

``` ruby Class
o = "teste" # Este é um valor
o.class # Retorna um objeto que representa a classe String
```

Se você estiver interessado na hierarquia de classe de um objeto, você pode perguntar para qualquer classe qual é sua superclasse:

``` ruby Class - Ruby 1.8
o.class # String: o é um objeto String
o.class.superclass # Object: superclasse de String é objeto
o.class.superclass.superclass # nil: Object não tem superclasse
```

No Ruby 1.9, Object já não é a verdadeira raiz da hierarquia de classes:

``` ruby Class - Ruby 1.9
# Ruby 1.9
Object.superclass # BasicObject: Object tem uma superclasse em 1.9
BasicObject.superclass # nil: BasicObject não tem nenhuma superclasse
```

Assim, uma forma particularmente simples para verificar a classe de um objeto é, por comparação direta:

``` ruby Class
o.class == String # true se o é uma String
```

O método instance_of? faz a mesma coisa e é um pouco mais elegante:

``` ruby Class
o.instance_of? String # verdade se o é uma String
```

Normalmente, quando testamos a classe de um objeto, também gostaríamos saber se o objeto é uma instância de qualquer subclasse
dessa classe. Para testar isso, use o método is_a?, ou seu sinônimo kind_of?

```ruby Class
x = 1 # Este é o valor que estamos trabalhando com
x.instance_of? Fixnum # verdade: é uma instância de Fixnum
x.instance_of? Numeric # false: instance_of? não verifica a herança
x.is_a? Fixnum # verdadeiro: x é um Fixnum
x.is_a? Integer # verdade: x é um número inteiro
x.is_a? Numeric # verdade: x é um numérico
x.is_a? Comparable # verdade: funciona também com módulos mixin
x.is_a? Object # verdadeira para qualquer valor de x
```

A classe Class define o operador === em tal modo que ele pode ser usado no lugar do is_a?:

``` ruby Class method ===
Numeric === x # verdade: x is_a Numérico
```

Essa expressão é exclusivo para Ruby e é, provavelmente, menos legível do que utilizando o mais tradicional método is_a?.

Cada objeto tem uma classe bem definida em Ruby, e que a classe nunca muda durante a vida útil do objeto. Um objeto
type, por outro lado, é mais fluido. O tipo de um objeto está relacionada à sua classe, mas a classe é apenas parte de um
tipo de objeto. Quando falamos sobre o tipo de um objeto, nós entendemos o conjunto de comportamentos que caracterizam o objeto.
Outra maneira é colocar o tipo de um objeto em um conjunto de métodos que podem responder.
(Esta definição torna-se recursiva, porque não é apenas o nome dos métodos que importam, mas também os tipos de argumentos que os
métodos podem aceitar.)

Na programação com Ruby, que muitas vezes não se preocupam com a classe de um objeto, nós só queremos saber se podemos invocar
algum método nele.  Considere-se, por exemplo, o operador <<. Arrays, strings, files e outros I/O relacionados ao definir as classes
isso como um operador de acréscimo. Se estamos escrevendo um método que produz produção textual, podemos escrever, genericamente,
a usar esse operador. Então, o nosso método pode ser invocado com qualquer argumento que implementa <<. Nós não nos importamos com a
classe do argumento, basta que possamos anexá-lo. Nós podemos testar para isto com o método respond_to? :

```ruby Class respond_to?
o.respond_to? :"<<" # Verdadeiro se o operador tem uma <<
```

A deficiência desta abordagem é que ela só verifica o nome de um método, não os argumentos para esse método. Por exemplo,
Fixnum e Bignum implementam << como um operador de deslocamento à esquerda e espera o argumento de ser um número em vez de uma string.
Objetos inteiros parecem ser "appendable" (adicionável) quando usamos um respond_to? de teste, mas que produzem um erro quando
adiciona um código em string. Não há uma solução geral para este problema, mas um recurso ad-hoc, neste caso,
é explicitamente excluir objectos numéricos com o método is_a? :

``` ruby Class
o.respond_to? :"<<" and not o.is_a? Numeric
```

Outro exemplo do tipo distinção-versus-classe é a classe StringIO[*](partir da Biblioteca padrão do Ruby). StringIO permite a
leitura e gravação das strings como se fossem Objetos de IO. StringIO[*] imita os objetos IO API-StringIO definem os mesmos métodos
que os objetos IO fazem. Mas StringIO não é uma subclasse de IO. Se você escrever um método que espera um argumento de fluxo,
e testa a classe do argumento com is_a? IO, em seguida, o método não funciona com argumentos StringIO.

[*] -> <a href="http://www.ruby-doc.org/stdlib-1.9.3/libdoc/stringio/rdoc/StringIO.html">StringIO</a>

É isso ai rubistas... A cada vez que leio sobre, me apaixono mais. :D