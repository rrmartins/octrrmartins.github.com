---
layout: post
title: "Classe Objects em Ruby 1.9.2 - Part V"
date: 2012-05-20 14:04
comments: true
categories:
- Object
- Ruby
- Ruby 1.8
- Ruby 1.9
- The Ruby Programming Language
---
<!--more-->
<p>Hoje vamos continuar falando de <a href="http://ruby-doc.org/core-1.9.3/Object.html">Objects</a>, é hora de nos aprofundar.</p>

<h1>Objetos</h1>

<h3>Conversão de objetos</h3>

Muitas classes de Ruby definem métodos que retornam uma representação do objeto como um valor de uma classe diferente.
O método to_s, para a obtenção de uma representação de um objeto, é provavelmente o mais comumente implementado e mais conhecido
dos métodos. As subseções a seguir descrevem várias categorias de conversões.

<h5>Conversões explícitas</h5>

As classes definem métodos de conversão explícitos para uso da aplicação de código que precisa de converter um valor para uma
outra representação. Os métodos mais comuns nesta categoria são to_s, to_i, to_f e to_a para converter a String, Integer, Float,
e array, respectivamente. Ruby 1.9 adiciona métodos to_c e to_r que se converterem ao <a href="http://ruby-doc.org/stdlib-1.9.2/libdoc/syck/rdoc/Complex.html">Complex</a> e <a href="http://ruby-doc.org/stdlib-1.9.2/libdoc/bigdecimal/rdoc/Rational.html">Rational</a>.

Métodos built-in normalmente não chamam esses métodos para você. Se você chamar um método que espera uma String e passar um objeto
de algum outro tipo, que o método não é esperado para converter o argumento com to_s. (Valores interpolados em aspas dupla em strings,
no entanto, são automaticamente convertidos com to_s.)

to_s é facilmente o mais importantes dos métodos de conversão, porque representações de seqüência de objetos são tão comumente
utilizadas em interfaces de usuário. Uma importante alternativa para to_s é o método inspect. to_s é geralmente destinados a retornar uma
representação legível do objeto, adequado para usuários finais. inspect, por outro lado, é destinados ao uso de depuração, e deve
retornar uma representação que é útil para os desenvolvedores de Ruby. O padrão do método inspect, herdado de Object, simplesmente chama
to_s.

<h5>As conversões implícitas</h5>

Às vezes, uma classe tem características fortes de uma outra classe. A classe Exception de Ruby representa um erro ou condição
inesperada em um programa e encapsula uma mensagem de erro. No Ruby 1.8, objetos Exception não são apenas conversível para strings,
que são strings como objetos e podem ser tratadas como se fossem strings em muitos contextos [*], Por exemplo:

``` ruby class Exception
# Ruby 1.8
e = Exception.new("não é realmente uma exceção")
msg = "Erro:" + e # concatenação de String com uma exceção
```

Como os objetos de exceção são strings, eles podem ser usados ​​com a cadeia no operador de concatenação. Isso não funciona com a
maioria das outras classes de Ruby. A razão pela qual os objetos de Exceção pode se comportar como Objetos String é que, em Ruby
1.8, Exception implementa o método to_str, implícito de conversão, e o operador + definido pela String chama esse método em seu
do lado direito do operando.

Outros métodos de conversão implícitos são to_int para objetos que querem ser inteiro, como to_ary, para objetos que quer ser
array, e to_hash para objetos que querem ser hash. Infelizmente, as circunstâncias em que estes métodos de conversão são implícitos
chamados não são bem documentados. Entre as classes embutidas, estes métodos de conversões implícitas que normalmente não são implementadas, tampouco.

Observamos anteriormente, de passagem, que o operador == pode realizar um tipo fraco de tipo de conversão ao testar a igualdade.
Os operadores == definidos pelo Array, String, e Hash para ver se o operando do lado direito é da mesma classe como o operando esquerdo.
Se assim for, eles se comparam. Se não, verifica se o operando do lado direito tem um to_str, to_ary, ou método to_hash.
Eles não invocam estes métodos, mas se eles existem, eles invocam o método == do operando do lado direito e permiti que ele se decida
se é igual ao do operando esquerda.

No Ruby 1.9, as classes String, Array, Hash, RegExp e IO definiem tudas um método de classe chamado try_convert. Este métodos
convertem seu argumento se definido um método implícito apropriado de conversão, ou retorna contrário nil . Array.try_convert(o)
retorna o.to_ary se o método define, caso contrário, retorna nil. Estes métodos são try_convert conveniente se você quiser escrever
métodos que permitem conversões implícitas no seu argumento.

<h5>Funções de conversão</h5>

O módulo de Kernel define quatro métodos de conversão que se comportam como funções globais de conversão. Estas funções de array, Float,
Integer e String, têm os mesmos nomes que as classes para que se convertam, e eles são incomuns em que eles começam com uma
letra maiúscula.

A função Array tenta converter seu argumento para um array chamando to_ary. Se esse método é não definido ou retorna nil, ou ele tenta
o método to_a. Se to_a não está definido retorna nulo, a função Array simplesmente retorna um novo array contendo o argumento como
seu único elemento.

A função Float converte argumentos numéricos para objetos Float diretamente. Para qualquer valor não numérico, que chama o método to_f.

A função Integer converte o argumento para um Fixnum ou Bignum. Se o argumento é um valor numérico, ele é convertido diretamente. Valores
de Float são truncados e não arredondado. Se o argumento é uma string, ele procura por um indicador de radix(um condutor 0 para octal,
0x para hexadecimal, ou 0b para binário) e converte a string em conformidade. Ao contrário String.to_i não permiti caracteres não
numéricos à direita. Para qualquer outro tipo de argumento, a função Integer tenta converter primeiro com to_int e depois com to_i.

Finalmente, a função String converte o argumento para uma string simplesmente chamando seu método to_s.

<h5>Tipo de operador Aritmético</h5>

Tipos numéricos definem um método de conversão chamado <a href="http://www.ruby-doc.org/core-1.9.2/Numeric.html#method-i-coerce">coerce</a>. A intenção deste método é o de converter o argumento para o mesmo tipo como o objeto numérico no qual o método é invocado, ou
para converter ambos os objetos para algum tipo mais geralmente compatíveis. O método de coerce sempre retorna uma matriz que tem dois
valores numéricos do mesmo tipo. O primeiro elemento do array é o valor convertido do argumento para coerce. O segundo elemento do
retornado do array é o valor(convertido, se necessário) em que foi invocado coerce:

``` ruby Coerc
1.1.coerce(1) # [1.0, 1.1]: coagir Fixnum para Flutuar
require "rational" # Use números racionais
r = Rational(1,3) # terceira Um como um número racional
r.coerce (2) # [Rational (2,1), Rational (1,3)]: Fixnum para Rational
```
O método coerce é usado pelos operadores aritméticos. O operador + definido por Fixnum não sabe sobre números Rational, por exemplo, e se
o operando do lado direito é um valor rational, não se sabe como adicionar. coerce fornece a solução. Operadores numéricos são escritos
de modo que, se eles não sabem o tipo do operando do lado direito, que invocam o método coerce do operando do lado direito, passando o
operando da esquerda como um argumento. Voltando ao nosso exemplo de adição de um Fixnum e um Rational, o método coerce de Rational
retorna um array de dois valores racionais. Agora, o operador + definido por Fixnum pode simplesmente invocar + sobre os valores no array.

<h5>Conversões de tipos Booleanos</h5>

Valores booleanos merecem uma menção especial no contexto de conversão de tipo. Ruby é muito rigoroso com seus valores booleanos: true e
falso têm métodos to_s, que retornam "true" e "falso", mas definem nenhum outro método de conversão. E não há nenhum método to_b
para converter os outros valores para Booleanos.

Em algumas Linguagens, é falsa a mesma coisa que 0, ou pode ser convertidos para 0. Em Ruby, os valores verdadeiros e falsos são os
seus próprios objetos distintos, e não existem conversões implícitas que convertem os outros valores para verdadeiro ou falso.
Esta é apenas metade da história, no entanto. Operadores booleanos de Ruby e de sua condicional e construções em loops que usam
expressões booleanas podem trabalhar com outros valores que o verdadeiro e o falso. A regra é simples: em expressões Boolean, qualquer
valor diferente de false ou nil se comporta como (mas não é convertida em) verdadeiro. nil, por outro lado se comporta como falsa.

Suponha que você queira testar se a variável x é nula ou não. Em algumas línguagens, você deve escrever explicitamente uma expressão
de comparação que avalia a verdadeira ou falso:

```ruby True ou False
if x != nil #  a expressão "x! = nil" retorna true ou false para o caso
  puts x # x Imprimir se ela é definida
end
```

Esse código funciona em Ruby, mas é mais comum simplesmente para tomar vantagem do fato de que todos os outros valores do que zero e
falso se comportam como verdadeiras:

``` ruby True ou False
if x # Se x é não-nula
  puts x # Em seguida, imprimi-lo
end
```

É importante lembrar que os valores como 0, 0.0, e uma string vazia "" comportam-se como verdadeiro em Ruby, que é surpreendente se você
está acostumado a linguagens como C ou JavaScript.

É isso amigos...

Até Mais..