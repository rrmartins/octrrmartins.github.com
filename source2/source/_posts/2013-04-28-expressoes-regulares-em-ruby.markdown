---
layout: post
title: "Expressões Regulares em Ruby"
date: 2013-04-28 13:52
comments: true
categories:
- Ruby
- Ruby 2.0
- Ruby 1.9.3
- Expressão Regular
- Eloquent Ruby
- String
---
<!--more-->
Olá amigos,

Ando meio sumido, mas pretendo voltar a fazer os posts, e dar continuidade nos estudos.

Volto falando um pouco de `Expressão Regular` em Ruby([1.9.3](http://ruby-doc.org/core-1.9.3/Regexp.html), [2.0](http://ruby-doc.org/core-2.0/Regexp.htm)), vou abordar as duas ultimas versões do Ruby, mas gostaria de deixar claro que a nova versão [2.0](http://ruby-doc.org/core-2.0/) é a que estou usando, e não esta me deixando na mão.

Em Ruby, a expressão regular, ou `Regexp`([1.9.3](http://ruby-doc.org/core-1.9.3/Regexp.html), [2.0](http://ruby-doc.org/core-2.0/Regexp.htm)), tem sua própria sintaxe literal especial. Para fazer uma expressão regular em Ruby que você encerra o seu padrão entre as barras. Então, em Ruby nossa expressão regular seria:

``` ruby Regexp
/\d\d:\d\d (AM|PM)/
```

Você usa o operador `=~` para testar se uma expressão regular corresponde a uma string. Assim, se quisermos corresponder à expressão regular acima com um tempo real que seria executado:

``` ruby Regexp
# 1.9.3 e 2.0
 > puts /\d\d:\d\d (AM|PM)/ =~ '10:24 PM'
=> 0
```

Que o zero está tentando nos dizer muitas coisas. Primeiro, ele está dizendo que a expressão correspondente, esta começando no
índice zero. Em segundo lugar, o zero está nos dizendo é que, quando você combinar uma expressão regular, Ruby verifica ao longo
da String, procurando um conjunto em qualquer lugar da string. Podemos ver a digitalização em ação com este exemplo seguinte:

``` ruby Regexp
# 1.9.3 e 2.0
 > puts /PM/ =~ '10:24 PM'
=> 6
```

Que seis é uma indicação de onde o Regexp encontrou, mas só depois de Ruby digitalizar bem a string. Se não houver
correspondência, então você não vai ter um retorno para o seu problema, de modo a que este:

``` ruby Regexp
# 1.9.3 e 2.0
 > /May/ =~ 'Sometime in June'
=> nil
```

Voltará `nil`. Desde o operador `=~` retorna um número quando se encontra uma correspondência e `nil` se não, você pode usar
conjuntos de expressões regulares como booleanos:

```ruby Regexp
# 1.9.3 e 2.0
 > the_time = '10:24 AM'
 > puts "É de manhã!" if /AM/ =~ the_time
=> É de manhã!
```

O operador `=~` também é ambidestro: Não importa se a string ou a expressão regular vem em primeiro lugar, para que pudéssemos
refazer o último exemplo, como:

```ruby Regexp
# 1.9.3 e 2.0
 > puts "É de manhã!" if '10:24 AM' =~ /AM/
=> É de manhã!
```

Como disse, as expressões regulares são por padrão `case sensitive`: `/AM/` não corresponde `/am/`. Felizmente, você pode
transformar esse `case sensitive` colocando um i no fim de sua expressão, de modo que este:

```ruby Regexp
# 1.9.3 e 2.0
 > puts "É de manhã!" if /AM/i =~ 'am'
=> É de manhã!
```

Irá imprimir algo!

Além de seu uso mais ou menos independente com o operador `=~`, expressões regulares também entram em jogo nos métodos de `string`
que envolvem a pesquisa. Assim, você pode passar uma expressão regular para o método [gsub](http://ruby-doc.org/core-2.0/String.html#method-i-gsub) da classe `string`, talvez para apagar todo o conteúdo de um documento:

```ruby Regexp
class Document
  # A maioria da classe omitida...

  def obscure_times!
    @content.gsub!( /\d\d:\d\d (AM|PM)/, '**:** **' )
  end
end
```

Expressão Regular é muito usado e em alguns momentos ajuda a reduzir bastante as condicionais.

Até mais... :)