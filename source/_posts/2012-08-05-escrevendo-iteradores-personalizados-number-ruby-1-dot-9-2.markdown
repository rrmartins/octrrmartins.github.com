---
layout: post
title: "Escrevendo iteradores personalizados #Ruby 1.9.2"
date: 2012-08-05 16:18
comments: true
categories:
- Ruby API
- Integer
- String
- Array
- Ruby
- Enumerable
- each
- Ruby 1.9
- The Ruby Programming Language
---
<!--more-->
<p>Hoje vamos continuar falando de <a href="http://www.ruby-doc.org/core-1.9.2/">Ruby</a>, é hora de nos aprofundar falando um pouco de `Escrevendo iteradores personalizados`... Estranho para alguns, mas, veremos que é simples!</p>

<h1>Escrevendo iteradores personalizados</h1>

A definição de característica de um método `Iterator`, é que ele invoca um bloco de código associado com a
invocação do método. Você faz isso com a declaração `yield`. O método que se segue é um iterador trivial que apenas
invoca o seu bloco duas vezes:

```ruby yield
def twice
  yield
  yield
end
```

Para passar valores de argumento para o bloco, veja a declaração do `yield` com uma lista separada por vírgulas de
expressões. Tal como acontece com invocação de método, os valores dos argumentos podem, opcionalmente, ser colocada
com parênteses. O iterador simples a seguir mostra uma utilização do `yield`:

```ruby Method
# Este método espera um bloco. Gera n valores da forma
# M * i + c, para i de 0 .. n-1, e os yield deles, um de cada vez,
# Para o bloco associado.
def sequencia(n, m, c)
  i = 0
  while (i < n) # loop n vezes
    yield m * i + c # Invocar o bloco, e passar um valor a ela
    i += 1 # Incrementa i de cada vez
  end
end

# Aqui está uma invocação desse método, com um bloco.
# Ela imprime os valores 1, 6 e 11
sequencia(3, 5, 1) {| y | puts y}
```

	Nomenclatura: rendimento e iteradores

	Dependendo da sua experiência em programação, você pode encontrar os
	termos "yield" e "iterator". O método de sequencia mostrado anteriormente é
	um bom exemplo claro de por que o 'yield' tem o nome do que ele faz. Depois de calcular cada número
	na sequencia, o método 'yield' de controle (e produz o número calculado) para o bloco,
	de modo que o bloco pode trabalhar com ele. Nem sempre é isso claro,
	no entanto, em algum código que possa parecer como se fosse o bloco que é
	produzindo um resultado de volta para o método que o invocou.

	Um método como sequencia que espera um bloco e invoca-lo várias vezes é chamado de
	'iterador', porque parece e se comporta como um loop. Isso pode ser confuso se você está
	acostumado a linguagens como Java em que iteradores são objetos. Em Java, o código que o cliente usa,
	o iterador está no controle e 'puxa' os valores do iterador quando ela precisa deles.
	Em Ruby, o método iterator está no controle e 'empurra' os valores para o bloco que quer.

	Este problema de nomenclatura está relacionada com a distinção entre 'Iteradores interno'
	e 'iteradores externos'.


Aqui está outro exemplo de um 'iterador' em Ruby; ele passa dois argumentos para o seu bloco. Vale notar
que a implementação deste iterador usa outro iterador internamente:

```ruby  yield
# Gera n pontos uniformemente espaçados em torno da circunferência de um
# Círculo de raio r centrado em (0,0). Rendimento da coordenadas X e Y
# De cada ponto ao bloco associado.
def circle(r,n)
  n.times do |i|    #  Observe que este método é implementado com um bloco
    angle = Math::PI * 2 * i / n
    yield r*Math.cos(angle), r*Math.sin(angle)
  end
end

# Esta invocação das impressões iterador:
# (1,00, 0,00) (0,00, 1,00) (-1,00, 0,00) (-0,00, -1,00)
circle(1,4) {|x,y| printf "(%.2f, %.2f)", x, y }
```

Usando a palavra-chave `yield` realmente é muito parecido com invocação de um método.
Parênteses nos argumentos são opcionais. Você pode usar `*` para expandir uma matriz de argumentos
individuais. `yield` ainda permite que você passe um hash literal sem as chaves ao seu redor. Ao contrário
de uma invocação de método, no entanto, uma expressão `yield` pode não ser seguido por um bloco.
Você não pode passar um bloco a um bloco.

Se um método é invocado sem um bloco, é um erro para o método `yield`, porque não há nada para `yield`. Às vezes
você querer escrever um método que produz a um bloco se for fornecido, mas tem alguma ação padrão (outro de lançar
um erro), se invocado com nenhum bloco. Para fazer isso, use `block_given?` para determinar se há um bloco
associado com a chamada. `block_given?`, e seu sinônimo `iterator?`, são métodos do Kernel, então eles agem como
funções mundiais. Aqui está um exemplo:

```ruby block_biven?
# Retorna um array com n elementos da forma m * i + c
# Se um bloco é dado, igualmente produzir cada elemento para o bloco
def sequence(n, m, c)
  i, s = 0, []                  # Inicializa variáveis
  while(i < n)                  # Loop n vezes
    y = m*i + c                 # calcula o valor
    yield y if block_given?
    s << y                      # armazena o valor
    i += 1
  end
  s             # Retorna o array de valores
end
```

É isso aí!

Até o proximo!