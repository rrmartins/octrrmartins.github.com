---
layout: post
title: "Invocações de método"
date: 2012-06-02 15:36
comments: true
categories: 
- Array
- Hash
- Variable
- String
- Ruby
- Ruby 1.8
- Ruby 1.9
- The Ruby Programming Language
---

<p>Hoje vamos continuar falando de <a href="http://www.ruby-doc.org/core-1.9.2/">Ruby</a>, é hora de nos aprofundar falando um pouco de Variáveis ​​não inicializadas</p>

<h1>Invocações de método</h1>

Uma expressão de invocação de método tem quatro partes:
<!-- more -->
* Uma expressão arbitrária cujo valor é o objeto no qual o método é chamado. Esta expressão é seguido por . ou :: para separá-lo a partir
  do nome do método que se segue. A expressão e o separador são opcionais; se omitido, o método é invocada em si mesmo.

* O nome do método a ser invocado. Este é o único pedaço exigido de uma expressão chamada de método.

* Valores com o argumento passado para o método. A lista de argumentos podem ser colocados entre parênteses, mas estes são geralmente
  opcionais. Se há mais de um argumento, eles são separados uns dos outros com vírgulas. O número e tipo de argumentos necessários
  dependem da definição do método. Alguns métodos podem ser chamodos sem argumentos.

* Um bloco opcional de código delimitado por chaves ou por um par <code>do/end</code>. O método pode invocar esse código usando a palavra-chave de 
   <code>yield</code>. Esta capacidade de associar código arbitrário com qualquer invocação de método é a base para métodos poderosos em Ruby. 

Um nome do método é normalmente separado do objeto sobre o qual é chamado com um <code>..</code>, e com o <code>::</code>, que também é permitido, mas é raramente
usado porque pode fazer invocações de método parecem mais com referênciar constantes expressões.

Quando o interpretador Ruby tem o nome de um método e um objeto em que deve ser invocado, ele encontra a definição adequada do método
chamado usando um processo conhecido como "método de pesquisa" ou "resolução de nome do método." Os detalhes não são importantes aqui,
mas eles são explicados completamente no método de pesquisa.

O valor de uma invocação expressão de método é o valor da expressão avaliada pelo último método no corpo do código. Aqui, no entanto, são
alguns exemplos de invocações de método:

```ruby Ruby
puts "Olá mundo" # "puts" invocado em auto, com uma seqüência de arg
Math.sqrt(2) # "sqrt" invocado em Matemática objeto com uma arg
message.length # "comprimento" invocado em mensagem objeto; não args
a.each{|x| p x} # "cada" chamado em um objeto, com um bloco associado
```

Um dos exemplos do método de invocação mostradas anteriormente foi <code>message.length</code>. Você pode ser tentado a pensar-lo como uma variável 
de expressão de referência, avaliando para o valor do comprimento da variável do objeto mensage. Este não é o caso, no entanto, Ruby tem
um modelo de programação muito orientada a objetos: objetos em Ruby podem encapsular qualquer número de variáveis ​​de instância interna,
mas expõem métodos apenas para o mundo exterior. Como o método <code>length</code> não espera argumentos e é chamado sem os parênteses opcionais,
parece que referência uma variável. Na verdade, isso é intencional. Métodos como estes são chamados para atribuir métodos de acesso, e
dizemos que o objeto da <code>mensage</code> tem um atributo de <code>lenght</code>. Como veremos, é possível que o objeto de <code>mensage</code>
para definir um método chamado <code>length=</code>. Se este método espera um único argumento, então é um método setter do atributo e
Ruby invoca em resposta a atribuição. Se um tal método é definido, então estas duas linhas de código seria tanto chamar o mesmo método:

```ruby Length
message.length=(3) # invocação do método tradicional
message.length = 3 # invocação Método como a atribuição
```

Agora, considere a seguinte linha de código, assumindo que uma variável contém um <code>Array</code>:

```ruby Array
a[0]
```

Você pode pensar novamente que este é um tipo especial de variável de referência, onde a variável em questão é na verdade um elemento do
<code>Array</code>. Novamente, no entanto, esta é invocação do método. O intérprete Ruby converte o acesso ao <code>Array</code> para isso:

```ruby Array
a.[](0)
```

O acesso ao <code>Array</code> torna-se uma invocação do método chamado <code>[]</code>, com o índice do <code>Array</code> como seu
argumento. Esta sintaxe de acesso de <code>Array</code> não se limita a <code>Arrays</code>. Qualquer objeto é permitido para definir um
método chamado <code>[]</code>. Quando o objeto é "indexado", com o indix entre parênteses, os valores dentro dos parênteses será passado
para o método. Se o método <code>[]</code> é escrito para esperar três argumentos, então você deve colocar três expressões separados por
vírgula dentro dos colchetes.

Atribuição de <code>Arrays</code> também é feito através de invocação de método. Se o objeto o define um método chamado <code>[]=</code>, então a expressão <code>o[x] = y</code> torna-se <code>o.[] = (x, y)</code>, e a expressão <code>o[x, y] = z</code> se torna <code>o.[] = (x , y, z)</code>.

Muitos operadores do Ruby são definidos como métodos, e expressões como <code>x + y</code> são avaliados como <code>x.+(y)</code>, onde o
nome de método é <code>+</code>. O fato de que muitos dos operadores do Ruby são definidos como métodos que significa você pode redefinir
esses operadores na sua próprias classes.

Agora, vamos considerar esta expressão muito simples:

``` ruby Ruby
x
```

Se uma variável x nomeada existe (Isto é, se o interpretador Ruby tem visto uma atribuição a x), então esta é uma variável de referência.
Se nenhuma variável existe, então esta é uma invocação do método x, sem argumentos.

A palavra <code>super</code> em Ruby é um tipo especial de invocação de método. Esta palavra é usada ao criar uma subclasse de outra
classe. Por si só, <code>super</code> passa os argumentos do método corrente para o método com o mesmo nome na superclasse. Ele também
pode ser usado como se fosse realmente o nome de um método e pode ser seguido por uma lista de argumentos arbitrários. A palavra-chave
<code>super</code> é abordada em detalhes no Aumentando Comportamento por encadeamento.

Até a Proxima... :)