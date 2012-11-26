---
layout: post
title: "Testando Associações em Range - Part II"
date: 2012-05-15 21:49
comments: true
categories: 
- Range
- Ruby
- Ruby 1.8
- Ruby 1.9
- The Ruby Programming Language
---

<p>Continuando os estudos de Ruby, e a leitura do livro The Ruby Programming Language</p>

<p>Hoje vamos continuar falando de Range, é hora de nos aprofundar.</p>

<h2>Teste Associação em um intervalo</h2>

A classe Range define métodos para determinar se um valor arbitrário é um membro de (isto é, está incluído no) um Range. Antes de entrar em
detalhes sobre estes métodos, é necessário explicar que associaçãoem range pode ser definido de duas maneiras diferentes que estão relacionadas com a diferença entre os intervalos contínuos e discretos. Um valor x é um membro do range entre begin..end pela primeira definição se:
<!-- more -->
``` ruby Range
begin <= x <= end
```

E X é um membro do range begin...end (com três pontos), se:

``` ruby Range
begin <= x < end
```

Todos os valores de ponto de extremidade deve implementar o operador <=>, assim que esta definição de associação funciona para qualquer objeto Range e não requer um ponto final para implementar o método succ. Esse é o teste de associação contínua.

A segunda definição de filiação discreta independe succ. Ele trata um intervalo begin..end como um conjunto que inclui begin, begin.succ, begin.succ.succ, e assim por diante. Por esta definição, a associação do Range é ajustada a associação, e um valor de x está incluído numa range apenas se for um valor retornado por uma das invocações de succ. Observe que os testes de associação discreta é potencialmente
uma operação muito mais cara do que os testes de associação contínua.

Com isso de fundo, podemos descrever os métodos de distribuição para testar a associação. Ruby 1.8 suporta dois métodos, include?
e member?. Eles são sinônimos, e ambos usam a associação contínua, teste:

```ruby Range
r = 0...100 # O intervalo de números inteiros de 0 a 99
r.member? 50 # => verdadeira: 50 é um membro da range
r.include? 100 # => falso: 100 está excluída a partir do intervalo
r.include? 99.9 # => verdadeira: 99,9 é inferior a 100
```

A situação é diferente no Ruby 1.9. Essa versão da linguagem introduz um novo método, cover?, que
funciona como include? e member? do Ruby 1.8, ele sempre usa o teste de associação contínua. 
include? e member? ainda são sinônimos no Ruby 1.9. Se os pontos finais do intervalo são números, estes métodos utilizam o
teste de associação contínua, assim como eles fizeram no Ruby 1.8. Se os desfechos não são numéricas, no entanto, eles ao invés de 
usar o teste de associação discreta. Podemos ilustrar estas mudanças com um Range discreto de Srintgs (você pode querer usar ri entender como String.succ funciona):

``` ruby ri String.succ
= String.succ

(from ruby site)
------------------------------------------------------------------------------
  str.succ   -> new_str
  str.next   -> new_str

------------------------------------------------------------------------------

Returns the successor to str. The successor is calculated by
incrementing characters starting from the rightmost alphanumeric (or the
rightmost character if there are no alphanumerics) in the string. Incrementing
a digit always results in another digit, and incrementing a letter results in
another letter of the same case. Incrementing nonalphanumerics uses the
underlying character set's collating sequence.

If the increment generates a ``carry,'' the character to the left of it is
incremented. This process repeats until there is no carry, adding an
additional character if necessary.

  "abcd".succ        #=> "abce"
  "THX1138".succ     #=> "THX1139"
  "<<koala>>".succ   #=> "<<koalb>>"
  "1999zzz".succ     #=> "2000aaa"
  "ZZZ9999".succ     #=> "AAAA0000"
  "***".succ         #=> "**+"


(END) 
```

```ruby Range
triplica = "AAA".."ZZZ"
triplica.include? "ABC" # verdade, rápido em 1.8 e lento em 1.9
triplica.include? "ABCD" # verdadeira em 1.8, false em 1.9
triplica.cover? "ABCD" # verdadeiro e rápido em 1.9
triplica.to_a.include? "ABCD" # false e lento em 1.8 e 1.9
```

Na prática, a maioria dos Ranges têm pontos de extremidade numéricos, e o Range de mudanças na API entre Ruby 1.8
e 1.9 têm pouco impacto.


É isso ai, até a proxima... :D

E bons estudos..