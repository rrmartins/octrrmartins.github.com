---
layout: post
title: "Classe Objects em Ruby 1.9.2 - Part III"
date: 2012-05-19 16:58
comments: true
categories: 
- Object
- Ruby
- Ruby 1.8
- Ruby 1.9
- The Ruby Programming Language
---

<p>Continuando os estudos de Ruby, e a leitura do livro The Ruby Programming Language</p>

<p>Hoje vamos continuar falando de <a href="http://ruby-doc.org/core-1.9.3/Object.html">Objects</a>, é hora de nos aprofundar.</p>

<h1>Objetos</h1>

<h3>Igualdade objeto</h3>

Ruby tem um número surpreendente de maneiras de comparar objetos para igualdade, e é importante entender como eles funcionam, assim você sabe quando usar cada método.
<!--more-->

<h3>O método equal? </h3>

O método equal? é definido pelo objeto para testar se os dois valores referem-se exatamente o mesmo objeto. Para qualquer dos dois
distintos objetos, esse método sempre retorna false:

``` ruby equal?
a = "Ruby" # Uma referência a um objeto String
b = c = "Ruby" # Duas referências a outro objeto String
a.equal?(b) # falsa: a e b são objetos diferentes
b.equal?(c) # verdadeiro: b e c referem-se ao mesmo objeto
```

Por convenção, nunca subclasses substituem o método equal?.

Outra maneira de determinar se os dois objetos são, de fato, do mesmo objetivo é verificar a sua object_id:

``` ruby equal?
a.object_id == b.object_id # Funciona como a.equal? ​​(b)
```

<h3>O operador ==</h3>

O operador == é a forma mais comum para testar a igualdade. Na Classe Object, é simplesmente uns testes de sinónimos para equal?, e
se duas referências de objeto são idênticos. A maioria das classes redefinem este operador para permitir instâncias distintas para ser 
testado para igualdade:

```ruby Operador ==
a = "Ruby" # uma string
b = "Ruby" # Um objeto String diferente com o mesmo conteúdo
a.equal?(b) # falsa: a e b não se referem ao mesmo objeto
a == b # verdade: mas estes dois objetos distintos têm valores iguais
```

Note que o único sinal de igual nesse código é o operador de atribuição. Leva dois sinais de igual para testar a igualdade
em Ruby (esta é uma convenção que compartilha Ruby com muitas outras linguagens de programação).

Padrão de Classes do Ruby definem o operador == para implementar uma razoável definição de igualdade. Isso inclui Array 
e as classes de hash. Dois Arrays são iguais de acordo com == se tiverem o mesmo número de elementos, 
e se os seus elementos correspondentes são todos iguais de acordo com ==. Dois hashes são == se contiverem o mesmo número de pares 
chave/valor, e se as chaves e valores são, eles próprios iguais. (Os valores são comparados com o operador ==, 
mas chaves de hash são comparados com o método eql?.)

	Igualdade para programadores Java

	Se você é um programador Java, você está acostumado a usar o operador == para testar se dois objetos são
    o mesmo objeto, e você está acostumado a usar o método equals para testar se dois objetos distintos têm o mesmo valor. 
    Convenção do Ruby é apenas sobre o oposto de Java.

As classes numéricas realizam conversões de tipo simples nas suas operações de ==, de modo que (por exemplo) o
Fixnum 1 e o Float 1.0 comparados como iguais. O operador == de classes, como String e Array, normalmente requerem dois operandos 
para ser da mesma classe. Se o operando do lado direito define uma to_str ou função de conversão to_ary, então estes operadores invocam 
o operador == definido pelo lado direito operando, e deixam que objeto decidem se é igual ao lado esquerdo, sendo string ou array. 
Assim, é possível (embora não comum) para definir classes com comportamento comparação de string ou array.

!= ("Não-igual") é usado em Ruby para testar a desigualdade. Quando o Ruby vê !=, Ele simplesmente usa o operador == e depois 
inverte o resultado. Isto significa que uma única classe precisa definir o operador == para definir a sua própria noção de
igualdade. Ruby dá o operador != de graça. No Ruby 1.9, no entanto, as classes podem explicitamente definir os seus próprios operadores !=.

<h3>O método eql?</h3>

O método eql? é definido pelo objeto como um sinônimo para equal?. Classes que se sobrepõem a ele normalmente usá ele como uma 
versão rígida do operador == que não faz nenhum tipo de conversão. Por exemplo:

```ruby eql?
1 == 1.0 # verdade: Fixnum e objetos flutuantes podem ser ==
1.eql (1,0) # falsa: mas eles nunca são eql!
```

A classe Hash usa eql? para verificar se duas chaves de hash são iguais. Se dois objetos são eql?, os métodos de hash também 
deve retornar o mesmo valor. Normalmente, se você criar uma classe e defini o operador ==, você pode simplesmente escrever um
método de hash e definir eql? para usar ==.

<h3>O operador ===</h3>

O operador === é comumente chamado de "caso de igualdade" e é usado para testar se o valor-alvo de uma declaração caso corresponde 
a qualquer das cláusulas, quando dessa declaração.

Classe Object define por padrão o operador === para que ele chama o operador ==. Para muitas classes, por conseguinte, a igualdade 
caso é o mesmo que == (igualdade). Mas certas classes principais definem === de maneira diferente, e nestes casos, é mais de um membro 
ou operador correspondente. Range define === para testar se um valor está dentro do intervalo. Regexp define === para testar se uma 
string corresponde à expressão regular. E Class define === para testar se um objeto é uma instância dessa Class. No Ruby 1.9, Symbol
define === retornando true se o operando do lado direito é o mesmo símbolo como o esquerda ou se é uma cadeia guardando o mesmo texto.
Exemplos:

``` ruby Operador ===
(1..10) === 5 # verdadeiro: 5 está na gama de 1 .. 10
/\d+/ === "123" # verdade: a seqüência corresponde à expressão regular
String === "s" # verdade: "s" é uma instância da classe String
:s === "s" # verdadeiro no Ruby 1.9
```

É raro ver o operador === usado explicitamente como este. Mais comumente, a sua utilização é simplesmente implícita em uma instrução case.

<h3>O operador =~</h3>

O operador =~ é definido pela String e Regexp (e Symbol no Ruby 1.9) para realizar ligações de padrões, e isso não é realmente um
operador de igualdade em tudo. Mas isso não tem um sinal de igual na mesma, de modo que ele está aqui para ser completo. 
Objeto define uma versão sem o operador de =~, que sempre retorna false. Você pode definir este operador em sua própria classe, 
se essa classe define um tipo de correspondência de operação padrão ou tem uma noção de igualdade aproximada, por exemplo. 
!~ é definido como o inverso da =~. É definível em Ruby 1.9 mas não no Ruby 1.8

É isso ai rubistas... Até a proxima!