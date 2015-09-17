---
layout: post
title: "Blocos em #Ruby 1.9"
date: 2012-08-19 14:02
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
<!--more-->
<p>Hoje vamos continuar falando de <a href="http://www.ruby-doc.org/core-1.9.2/">Ruby</a>, é hora de nos aprofundar falando um pouco de `Blocks`... Estranho para alguns, mas, veremos que é simples!</p>

<h1>Blocos em #Ruby 1.9</h1>

O uso de blocos é fundamental para o uso de iteradores. As subseções a seguir explicam:

* A sintaxe para associar um bloco com uma invocação de método
* O "valor de retorno" de um bloco
* O escopo de variáveis ​​em blocos
* A diferença entre os parâmetros dos blocos e parâmetros de método

<h3>Sintaxe bloco</h3>

Os `Blocos` não podem ser autônomo, pois eles são apenas legal após uma chamada de método. Você pode, no entanto, colocar um
bloco depois de qualquer invocação método, se o método não é um iterador e nunca invoca o bloco com a produção, o bloco será
ignorado. Blocos são delimitados por chaves ou com palavras-chave `do/end`. A abertura de chave ou a palavra-chave deve ser na
mesma linha com a invocação de método, ou então Ruby interpreta a linha como um terminador de instrução e invoca o método sem
o bloco:

``` ruby Blocos
# Imprima os números 1 a 10
1.upto(10) {|x| puts x } # Invocação e bloco em uma linha com aparelho
1.upto(10) do |x|        # Delimitado bloco com do/end
  puts x
end
1.upto(10) 				 # Bloco não especificado
  {|x| puts x } 		 # Erro na Sintaxe: bloco é permitido não depois de uma invocação
```

Uma convenção comum é a utilização de chaves quando um bloco se encaixa em uma única linha, e para usar `do/end` quando o bloco
se estende sobre linhas.  Isso não é totalmente uma questão de convenção, no entanto, o analisador de Ruby liga firmemente
para o símbolo que precede. Se você omitir os parênteses em torno de argumentos de método e usar delimitadores de chaves para
um bloco, em seguida, o bloco vai ser associado com o último argumento do método em vez do método em si, o que não é,
provavelmente, o que deseja. Para evitar neste caso, coloque entre parênteses os argumentos ou delimite o bloco com `do` e
`end`:

``` ruby Blocks
1.upto(3) {|x| puts x} # Parens e encaracolado chaves trabalhar
1.upto 3 do |x| puts x end
1.upto 3 {|x| puts x} # erro de sintaxe: tentando passar um bloco para 3!
```

Os blocos podem ser parametrizados, assim como métodos podem. Os parâmetros dos blocos são separados por vírgulas e delimitados
com um par de barra vertical (`|`), mas eles são de outra maneira muito parecida com os parâmetros do método:

```ruby Blocks
# O iterador Hash.each passa dois argumentos para seu bloco
hash.each do |chave, valor| # Para cada par (chave, valor) no hash
  puts "#{chave}: #{valor}" # Imprimir a chave eo valor
end # Fim do bloco
```

É uma convenção comum para escrever os parâmetros do bloco, na mesma linha, como a invocação do método e da chave de abertura
ou fazer palavra-chave, mas isto não é requerido pela sintaxe.


<h3>O Valor de um Bloco</h3>

Considerando o método `Array.sort`. Se você associar um bloco com uma invocação deste método, ele irá produzir pares de
elementos para o bloco, e é o trabalho do bloco para classificá-los.

O valor do bloco de retorno `(-1, 0 ou 1)` indica a ordem dos dois argumentos. O "valor de retorno" do bloco está disponível
com o método `iterator` como o valor da declaração `yield`.

O "valor de retorno" de um bloco é simplesmente o valor da última expressão avaliada no bloco. Assim, para classificar um `array` de palavras maior de a menor, poderíamos escrever:

```ruby Metodo sort
# O bloco tem duas palavras e "retorna" a sua ordem relativa
words.sort! {| X, y | y.length <=> x.length}
```

Nós estamos colocando a expressão "valor de retorno" entre aspas por uma razão muito importante: você não deve usar normalmente
a palavra-chave `return` para retornar um bloco. Um `return` dentro de um bloco faz com que o método que o contém (não o método
iterador que pertence ao bloco, mas o método que o bloco faz parte) o retorno naquela linha, o Ruby retorna a ultima linha do
bloco. Há, é claro, tem momentos em que isso é exatamente o que você quer fazer. Mas não use o `return` se você quer apenas
voltar de um bloco para o método que chamou `yield`. Se você precisar forçar um bloco para voltar ao método invocando antes que
ele atinja a última expressão, ou se você deseja retornar mais de um valor, você pode usar `next` em vez de retorno. Aqui é um
exemplo que usa `next` para voltar a partir do bloco:

```ruby Usando next
array.collect do |x|
  next 0 if x == nil  # Retorna se x é nulo
  next x, x*x         # Retorna dois valores
end
```

Note-se que não é particularmente comum usar `next` desta maneira, e o código acima é facilmente reescrito assim:

``` ruby Reescrevendo bloco
array.collect do |x|
  if x == nil
    0
  else
    [x, x*x]
  end
end
```

<h3>Blocos e escopo de variáveis</h3>

Blocos que definem um novo escopo de variáveis: variáveis ​​criadas dentro de um bloco só existem dentro desse bloco e estão são
indefinidas fora do bloco. Seja cauteloso, no entanto, as variáveis ​​locais em um método estão disponíveis para todos os blocos
dentro desse método. Então, se um bloco atribui um valor a uma variável que já está definida fora do bloco, este não cria uma
variável local nova, mas em vez disso, atribui um novo valor para a variável já existente. Às vezes, isso é exatamente o
comportamento que queremos:

``` ruby Escopos
total = 0
data.each {|x| total += x }  # Some os elementos da matriz de dados
puts total                   # Impressão do total da soma
```

Às vezes, no entanto, nós não queremos alterar as variáveis ​​no escopo delimitador, mas fazemos acidentalmente. Este problema é
uma preocupação particular para os parâmetros dos blocos em Ruby 1.8. No Ruby 1.8, se um parâmetro do bloco compartilha o nome
de uma variável existente, então invocações do bloco simplesmente atribuir um valor a essa variável já existente em vez de
criar uma variável de bloco local novo. O seguinte código, por exemplo, é problemático porque utiliza o mesmo identificador `i`
como o parâmetro para dois blocos nested blocos:

``` ruby Usando mesma variavel, Ruby 1.8
1.upto(10) do |i|         # 10 linhas
  1.upto(10) do |i|       # Cada um tem 10 colunas
    print "#{i}"         # Imprimi o número de colunas
  end
  print " ==> Row #{i}\n" # Número de linhas
end
```

Ruby 1.9 é diferente: os parâmetros dos blocos são sempre locais para o seu bloco, e invocações do bloco nunca atribuem valores
a variáveis ​​existentes. Se o Ruby 1.9 é invocado com o flag -w, ele irá avisá-lo se um parâmetro do bloco tem o mesmo nome de
uma variável existente. Isso ajuda a evitar escrever código que funciona de forma diferente em 1.8 e 1.9.

Ruby 1.9 é diferente de outra maneira importante, também. Sintaxe do bloco foi estendida para permitir que você declare
variáveis de blocos locais ​​que são garantidas para ser local, mesmo se uma variável com o mesmo nome já existe no escopo
delimitador. Para fazer, siga a lista de parâmetros do bloco com um ponto e vírgula e uma lista separada por vírgulas de
variáveis ​​do bloco local. Aqui está um exemplo:

``` ruby Variaveis
x = y = 0            # variáveis ​​locais
1.upto(4) do |x;y|   # x e y são locais para bloquear
                     # x e y são "sombra" das variáveis ​​externas
  y = x + 1          # Usa y como uma variável que vai receber valores
  puts y*y           # imprime 4, 9, 16, 25
end
[x,y]                # => [0,0]: o bloco não altera essas variaveis.
```

Neste código, x é um parâmetro de bloco: ele recebe um valor quando o bloco é invocado com a produtividade. y é uma variável de
bloco-local. Ele não receber qualquer valor a partir de uma chamada `yield`, mas tem o valor nil até que o bloco de fato
atribui outro valor a ele. O ponto de declarar essas variáveis locais no ​​bloco é garantir que você não vai acidentalmente
sub-escrever o valor de alguma variável existente. Se você chamar Ruby 1.9 com o flag -w, ele irá avisá-lo se um bloco de
variável local de uma variável existente.

Os blocos podem ter mais do que um parâmetro e mais de uma variável local, é claro. Aqui é um bloco com dois parâmetros e
três variáveis ​​locais:

``` ruby Variavel Local
hash.each {|key,value; i,j,k| ... }
```

<h3>Passando argumentos para um bloco</h3>

Nós dissemos anteriormente que os parâmetros para um bloco são muito parecidos com os parâmetros de um método. Eles não são
rigorosamente iguais, no entanto. Os valores de argumento que seguem a palavra-chave `yield` são designados para bloquear
parâmetros seguindo as regras que estão mais próximos as regras para a atribuição de variável do que as regras para a invocação
de método. Assim, quando um iterador executa `yield k,v`, para invocar um bloco declarado com parâmetros `|key, value|`, é
equivalente a esta instrução de atribuição:

```ruby Atribuição de parametros
key,value = k,v
```

O iterador `Hash.each_pair` produz um par `key/value` como este:

``` ruby Hash.each_pair
{:one=>1}.each_pair {|key,value| ... } # key=:one, value=1
```

No Ruby 1.8, é ainda mais claro que a invocação do bloco usa atribuição de variável. Lembre-se que no Ruby 1.8, os parâmetros
são apenas locais para o bloco se não estiverem já em uso, como variáveis ​​locais do método que a contém. Se eles já são
variáveis locais, em seguida, eles são simplesmente atribuídos. Na verdade, Ruby 1.8 permite que qualquer tipo de variável seja
utilizada como um parâmetro de bloco, incluindo variáveis ​​globais e variáveis ​​de instância:

```ruby Variaveis Locais e de Instância
{:one=>1}.each_pair {|$key, @value| ... } # Não funciona mais no Ruby 1.9
```

Este iterator define a variável global `$key` para `:one` e define a variável de instância `@value` para `1`. Como já
mencionado, o Ruby 1.9 faz os parâmetros dos blocos locais para o bloco. Isto também significa que os parâmetros dos blocos não
podem mais ser variáveis ​​globais ou de instância.

Os iteradores `Hash.each` segue um pares de `key/value` como dois elementos de uma única matriz. É muito comum, no entanto,
para ver o código como esta:

```ruby Hash.each
hash.each {|k,v| ... }  # Chave e valor atribuído a params k e v
```

Isso também funciona por atribuição paralela. O valor cedido, um conjunto de dois elementos, é atribuído às variáveis ​​k e v:

```ruby Atribuição
k, v = [value, key]
```

Pelas regras de atribuição paralela (ver <a href="{{ root_url }}/blog/2012/06/11/atribuicao-paralela-number-ruby/">Atribuição Paralela</a>), uma única matriz da direita é expandida e seus elementos atribuídos às variáveis ​​múltiplas do lado esquerdo.

Bloco de invocação não funciona exatamente como atribuição paralela. Imagine um iterador que passa dois valores ao seu bloco.
Por as regras de atribuição paralela, podemos esperar para ser capaz de declarar um bloco com um único parâmetro e ter os dois
valores automaticamente preenchido em uma matriz para nós. Mas não é assim que funciona:

``` ruby Parametros
def two; yield 1,2; end # Um iterador que produz dois valores
two {|x| p x }          # Ruby 1.8: avisa e impressões [1,2],
two {|x| p x }     		# Ruby 1.9: imprime 1, nenhum aviso
two {|*x| p x }    		# versão Ou: impressões [1,2]; nenhum aviso
two {|x,| p x }    		# versão Ou: impressões 1; nenhum aviso
```

No Ruby 1.8, vários argumentos são embalados em uma matriz, quando existe um parâmetro único no bloco, mas isso é obsoleto e
gera uma mensagem de aviso. No Ruby 1.9, o primeiro valor é atribuído ao parâmetro do bloco e o segundo valor é descartado. Se
queremos que vários valores podem a ser embalado em uma matriz e atribuído a um único parâmetro do bloco, nós devemos indicar
explicitamente que estamos prefixando o parâmetro com um `*`, exatamente como faríamos em uma declaração de método. Observe
também que podemos descartar explicitamente o segundo valor ao declarar uma lista de parâmetros de bloco, que termina com uma
vírgula, como se dissesse:
	"Há um outro parâmetro, mas não é usado e eu não posso ser incomodado para escolher um nome para ele."

Embora invocação de bloco não se comporta como a atribuição paralela, neste caso, não se comportam como uma chamada de método,
também. Se declarar um método com um argumento e depois passar dois argumentos para ele, o Ruby não vai apenas imprimir um
aviso, vai também gerar um erro.

A declaração do `yield` permite que `hashes` "nus" como o valor do último argumento, assim como invocações de métodos. Isto é,
se o último argumento para produzir é um literal de `hash`, você pode omitir as chaves. Porque não é comum para iteradores para
produzir `hashes`, temos que maquinar um exemplo para demonstrar isto:

``` ruby Hashes sem chaves
def hashiter; yield :a=>1, :b=>2; end  # Nota final sem chaves
hashiter {|hash| puts hash[:a] }       # Imprime 1
```

<h3>Bloquear Parâmetros no Ruby 1.9</h3>

No Ruby 1.8, apenas o último parâmetro do bloco pode ter um prefixo `*`. Ruby 1.9 levanta essa restrição e permite qualquer
parâmetro do bloco, independentemente da sua posição na lista, para ter um prefixo `*`:

``` ruby Prefixo *
def five; yield 1,2,3,4,5; end     # Seguindo os 5 valores
five do |head, *body, tail|        # Valores extras entrar em matriz corpo
  print head, body, tail           # Mostra "1 [2,3,4] 5"
end
```

No Ruby 1.9 parâmetros do bloco podem ter valores padrões, apenas como parâmetros de método pode. Suponha, por exemplo, que
você deseja iterar os valores de um objeto, mas o que você não sabe se o obejeto é um `array` ou um `hash`. Você pode usar um
bloco como este:

```ruby Variavel com um valor pré determinado
o.each {|key=nil,value| puts value}
```

Se cada iterador produz um único valor, é atribuído ao segundo parâmetro do bloco. Se cada um produz um par de valores,
são atribuídos a ambos os parâmetros.

No Ruby 1.9, o parâmetro do bloco final pode ser prefixado com e para indicar que vai receber qualquer bloco associado com a
invocação do bloco. Lembre, no entanto, que a invocação `yield` pode não ter um bloco associado.

Até o proximo assunto amigos.. :P
