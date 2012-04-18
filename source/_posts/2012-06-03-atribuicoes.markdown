---
layout: post
title: "Atribuições #Ruby"
date: 2012-06-03 11:55
comments: true
categories: 
- Variable
- String
- Array
- Ruby
- Ruby 1.8
- Ruby 1.9
- The Ruby Programming Language
---

<p>Hoje vamos continuar falando de <a href="http://www.ruby-doc.org/core-1.9.2/">Ruby</a>, é hora de nos aprofundar falando um pouco de Atribuições</p>

<h1>Atribuições</h1>

Uma expressão de atribuição especifica um ou mais valores para um ou mais valores. lvalue é o termo para algo que pode aparecer no lado
esquerdo de uma atribuição do operador. Os valores no lado direito de um operador de atribuição são algumas vezes chamado de rvalues ​​por
contraste. Variáveis, constantes, atributos e elementos do Array são lvalues ​​em Ruby. As regras para o significado de expressões de
atribuição são um pouco diferente para diferentes tipos de lvalues, e cada tipo é descrito em detalhe neste post.
<!--more-->

Existem três diferentes formas de expressões de atribuição em Ruby. Atribuição simples envolve um lvalue do operador <code>=</code>, e um rvalue.
Para exemplo:

```ruby Atribuição
x = 1 # Define o lvalue x para o valor 1
```

Atribuição abreviada é uma expressão abreviada que atualiza o valor de uma variável através da aplicação de alguma outro operador (tais
como a adição) ao valor atual da variável. Atribuição abreviada utiliza a atribuição de operadores como <code>+=</code> e <code>*=</code> que combinam operadores binários com uma sinal de igual:

``` ruby Atribuição
x += 1 # Define o lvalue x para o valor de x + 1
```

Por fim, a atribuição paralela é qualquer expressão de atribuição que tem mais do que um lvalue ou mais do que um rvalue. Aqui é um simples exemplo:

``` ruby Atribuição
x, y, z = 1,2,3 # Definir x a 1, y a 2 e z a 3
```

Atribuição paralela é mais complicado quando o número de lvalues não é o mesmo que o número de rvalues ​​ou quando existe um `array` sobre à direita.

O valor de uma expressão de atribuição é o valor (ou um `array` dos valores) atribuído. Além disso, o operador de atribuição 
é "Associativo à direita", se aparecer várias atribuições em uma única expressão, eles são avaliados da direita para a esquerda.
Isto significa que a atribuição pode ser acorrentado para atribuir o mesmo valor para as múltiplas variáveis:

```ruby Atribuição
x = y = 0 # X e Y recebem 0
```

Note-se que este não é um caso de atribuição paralela é duas atribuições simples, acorrentados juntos: Y é atribuído o valor 0, e então x é atribuído o valor da primeira tarefa (também 0).


<h5>Atribuindo a Variáveis</h5>

Quando nós pensamos em atribuição, geralmente pensamos em variáveis ​​e, na verdade, estes são os mais comuns em expressões de atribuição de lvalues. Lembre-se que Ruby tem quatro tipos de variáveis: variáveis ​​locais, variáveis ​​globais, variáveis ​​de instância e variáveis ​​de classe. Estes são distintas um do outro, pelo primeiro carácter no nome da variável. Atribuição funciona da mesma forma para todos os quatro tipos de variáveis, de modo que não é necessário fazer a distinção entre os tipos de variáveis ​​aqui.

Tenha em mente que as variáveis ​​de instância de objetos do Ruby são nunca nomes visíveis fora do objeto, e variável nunca é qualificado com um nome de objeto. Considere esta atribuição:

``` ruby Atribuição
ponto.x, ponto.y = 1, 2
```

Os lvalues ​​nesta expressão não são variáveis, são atributos.

Atribuição a uma variável funciona como seria de se esperar: a variável é simplesmente definida como o valor especificado. Ruby não tem nenhuma sintaxe para declarar explicitamente uma variável: variáveis ​​simplesmente vir à existência quando são atribuídas. Assim, uma  expressão simples como X poderia se referir a uma variável local chamada x ou um método chamado x. Para resolver essa ambigüidade, Ruby trata de um identificador como uma variável local se tem visto qualquer trabalho anterior para a variável. Ele faz isso com uma atribuição nunca foi executada. O código a seguir demonstra isso:

```ruby Atribuição
class Ambiguous  
  def x; 1; end # Um método chamado "x". Sempre retorna 1  

  def teste    
    puts x # Nenhuma variável foi visto, refere-se ao método acima: imprime 1    

    # A linha abaixo não é avaliada, por causa da cláusula "if falsa". 
    # Mas o analisador vê e trata x como uma variável para o resto do método.    
    x = 0 if false

    puts x # x é uma variável, mas nunca foi atribuído: irá imprimir nil

    x = 2 # Esta atribuição não é avaliada
    puts x # Então agora esta linha imprime 2  
  end
end
```


<h5>Atribuindo a Constantes</h5>

Constantes são diferentes das variáveis ​​de uma forma óbvia: seus valores são destinados a permanecer constante durante a execução de um programa. Portanto, existem algumas regras especiais para a atribuição de constantes:

	* Atribuição a uma constante que já existe, faz com o Ruby emita um aviso. O Ruby executa a tarefa,
	no entanto, que significa que as constantes não são realmente constante.

	* Atribuição a constantes não é permitido dentro do corpo de um método. Ruby assume que os métodos 
	destinados a ser invocados mais de uma vez, se você poderia atribuir a uma constante em um método, 
	que método ira emitir alertas a cada requisição após a primeira.  Então, isso simplesmente não é 
	permitido.

Ao contrário das variáveis, as constantes não vem a existir até que o `Intérprete Ruby` realmente executa a expressão de atribuição. A expressão não avaliada abaixo, não cria uma constante:

```ruby Atribuiçao de Constante
N = 100 if false
```

Note-se que isto significa que uma constante nunca está em um estado de inicialização. Se uma constante existe, então ele tem um valor atribuído a ela. A constante só terá valor se o valor realmente for `nil`.



<h5>Atribuindo a Atributos e elementos de Array</h5>

Atribuição a um atributo ou um elemento de um `Array` é na verdade uma abreviação do Ruby para a invocação método. Suponha que um objeto tem um método chamado `m=:`, o nome do método tem um sinal de igual como seu último caractere. Em seguida, `o.m` pode ser usado como um lvalue em uma expressão de atribuição. Suponha, além disso, que o valor v é atribuído:

```ruby Atribuição
o.m = v
```

O interpretador Ruby converte essa tarefa para a seguinte invocação de método:

```ruby Atribuição
o.m=(v) # Se omitir os parênteses e adicionar um espaço, isso parece tarefa!
```

Isto é, ele passa o valor v para o método `m=`. Esse método pode fazer o que quiser com o valor. Normalmente, ele irá verificar que o valor é de desejado tipo e dentro da gama desejada, e, em seguida, armazená-lo em uma variável de instância do objeto. Métodos como `m=` são geralmente acompanhados de um método `m`, que simplesmente retorna o valor mais recentemente passado para `m=`. Alguns dizem que `m=` é um método setter e `m` é um método getter. Quando um objeto tem esse par de métodos, dizemos que ele tem um atributo `m`. Atributos são às vezes chamado de "Propriedades" em outras línguas. 

Atribuindo valores aos elementos de um `Array` também é feito pelo método de invocação. Se um objeto define um método chamado `[]=` (nome do método é apenas esses três caracteres de pontuação) que espera dois argumentos, então na expressão o `[x] = y` é realmente executado como:

```ruby Atribuição
o.[]= (x, y)
```

Se um objeto tem um método `[]=` que espera três argumentos, então ele pode ser indexado com dois valores entre os colchetes. As dois seguintes expressões são equivalentes neste caso:

```ruby Atribuição
o[x, y] = z
o.[] = (x, y, z)
```

<h5>Atribuição Abreviada</h5>

Atribuição Abreviada é uma forma de atribuição que combina atribuição com alguns outras operações. Ele é usado mais comumente para incrementar variáveis:

```ruby Atribuição Abreviada
x += 1
```

O `+=`, não é um operador real de Ruby, e a expressão acima é simplesmente uma abreviatura para:

```ruby Atribuição Abreviada
x = x + 1
```

Atribuição Abreviada não pode ser combinado com paralelo de atribuição: ela só funciona quando há um único lvalue à esquerda e um único valor do lado direito. Ela não deve ser usado quando o lvalue é uma constante, porque ele vai reatribuir a constante e causar um aviso. Atribuição abreviada pode, contudo, ser utilizado quando o lvalue é um atributo. As duas expressões a seguir são equivalentes:

```ruby Atribuição Abreviada
o.m += 1
o.m=(o.m()+1)
```

Atribuição abreviada funciona até mesmo quando o lvalue é um elemento de um `array`. Estas duas expressões são equivalente:

```ruby Atribuição Abreviada
o[x] -= 2
o.[]=(x, o.[](x) - 2)
```

Observe que esse código usa `-=` em vez de `+=`. Como você poderia esperar, o `-=` é pseudooperator que subtrai seu rvalue a partir do seu lvalue.

Além `+=` e `-=`, há outros 11 pseudooperators que podem ser usados ​​para atribuição abreviada. Eles são listados abaixo. Note-se que estes não são verdadeiros operadores próprios, eles são simplesmente uma abreviação para expressões que usam outros operadores. Muitos desses outros operadores são definidos como métodos. Se uma classe define um método chamado `+`, por exemplo, em seguida, que as alterações do sentido da Atribuição abreviada com `+=` para todas as instâncias dessa classe.

	Atribuição                          Expansão
	x += y                              x = x + y
	x -= y                              x = x - y
	x *= y                              x = x * y
	x /= y                              x = x / y
	x %= y                              x = x % y
	x **= y                             x = x ** y
	x &&= y                             x = x && y
	x ||= y                             x = x || y
	x &= y                              x = x & y
	x |= y                              x = x | y
	x ^= y                              x = x ^ y
	x <<= y                             x = x << y
	x >>= y                             x = x >> y


É isso aí amigos... até o proximo post!