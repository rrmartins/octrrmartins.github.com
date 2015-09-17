---
layout: post
title: "Atribuição Paralela #Ruby"
date: 2012-06-11 19:36
comments: true
categories:
- Enumerable
- Variable
- String
- Array
- Ruby
- Ruby 1.8
- Ruby 1.9
- The Ruby Programming Language
---
<!--more-->
<p>Hoje vamos continuar falando de <a href="http://www.ruby-doc.org/core-1.9.2/">Ruby</a>, é hora de nos aprofundar falando um pouco de Atribuições Paralelas</p>

<h1>Atribuições Paralelas</h1>

Atribuição paralela é qualquer atribuição de expressão que tem mais do que um `lvalue`, mais do que um `rvalue`, ou ambos. Múltiplos `lvalues` e ​​múltiplos `rvalues` são separados uns dos outros com uma vírgula. `lvalues` e `rvalues` ​​pode ser prefixado com `*`, que às vezes é chamado de operador `splat`, embora não seja um verdadeiro operador. O significado de `*` é explicado mais adiante neste post.

Expressões de atribuição mais paralelas são simples, e é óbvio que eles significam. Existem alguns casos complicados, no entanto, e os subtitulos seguintes explicam todas as possibilidades.

<h5>Mesmo número de lvalues ​​e rvalues</h5>

Atribuição paralela é a sua forma mais simples quando não são os mesmos número de `lvalues` ​​e `rvalues`:

``` ruby Atribuição de Mesmo número
x, y, z = 1, 2, 3 # x = 1; y = 2; z = 3
```

Neste caso, o primeiro `rvalue` é atribuído ao primeiro `lvalue`; o segundo `rvalue` é atribuído ao segundo `lvalue`, e assim por diante.

Estas atribuições são efetivamente executadas em paralelo, não seqüencialmente. Por exemplo, as seguintes duas linhas não são as mesmas:

``` ruby Atribuição
x, y = y, x # Paralela: trocar o valor de duas variáveis
x = y; y = x # seqüencial: ambas as variáveis ​​têm o mesmo valor
```

<h5>Um lvalue, ​​múltiplos rvalues</h5>

Quando há um único `lvalue` e mais do que um `rvalue`, Ruby cria um `array` para armazenar os `rvalues` ​​e atribui esse `array` para o `lvalue`:

``` ruby Um lvalue e Múltiplos rvalues
x = 1, 2, 3 # x = [1,2,3]
```

Você pode colocar um `*` antes do lvalue sem alterar o significado ou o valor de retorno desta atribuição.

Se você quiser impedir que os ​múltiplos `rvalues` de serem combinados em um único `array`, siga o `lvalue` com uma vírgula. Mesmo sem um lvalue depois da vírgula, isto torna que o Ruby se comporte como se há ​​múltiplos lvalues:

``` ruby Atribuições
x, = 1, 2, 3 # x = 1; outros valores são descartados
```

<h5>M​últiplos lvalues, array único de rvalue</h5>

Quando existem múltiplos `lvalues​` e apenas um único `rvalue`, Ruby tenta expandir o rvalue em uma `lista` de valores a atribuir. Se o `rvalue` é um `array`, Ruby expande o `array` para que cada elemento torna-se seu próprio `rvalue`. Se o `rvalue` não é um `array`, mas implementa um método `to_ary`, Ruby invoca esse método e, em seguida, se expande o `array` no retorno:

```ruby Atribuição
x, y, z = [1, 2, 3] # mesmo que x, y, z = 1,2,3
```

A atribuição paralela foi transformado de modo que há ​​múltiplos `lvalues` e zero (se expandida o `array` estando `empty`) ou mais `rvalues`. Se o número de `lvalues` ​​e `rvalues` ​​são os mesmos, então a atribuição ocorre conforme descrito anteriormente, Mesmo número de lvalues ​​e rvalues`. Se os números são diferentes, então a atribuição ocorre como descrito a seguir em `Diferentes números de lvalues ​​e rvalues`.

Podemos usar o truque de fuga por vírgula descrito acima para transformar uma sessão ordinária não paralela em uma atribuição que automaticamente descompacta um `array` à direita:

``` ruby Atribuição
x = [1,2] # x torna-se [1,2]: isso não é atribuição paralela
x, = [1,2] # x torna-se 1: da vírgula torna paralelo
```

<h5>Diferentes números de lvalues ​​e rvalues</h5>

Se houver `lvalues` mais do que ​​`rvalues`, e nenhum operador `splat` é envolvido, em seguida, o primeiro `rvalue` é atribuído ao primeiro `lvalue`, o segundo `rvalue` é atribuído ao segundo `lvalue`, e assim por diante, até todos os `rvalues` ​​forem atribuídos. Em seguida, cada um dos restantes `lvalues` ​​é atribuído nil, substituindo qualquer valor existente para que `lvalue`:

``` ruby Atribuição
x, y, z = 1, 2 # x = 1; y = 2; z = nil
```

Se houver `rvalues` mais do que ​`​lvalues`, e nenhum operador `splat` é envolvido, em seguida, os `rvalues` ​são atribuídos em ordem a cada um dos `lvalues`, e os restantes `rvalues` são descartados :

```ruby Atribuição
x, y = 1, 2, 3 # x = 1; y = 2, 3 não é atribuído
```

<h5>O operador splat</h5>

Quando um `rvalue` é precedido por um asterisco(`*`), isso significa que o referido valor é um `array` e que seus elementos devem ser cada um `rvalues`. Os elementos do `array` substituem o `array` na lista original do `rvalue`, e a atribuição procede como descrito abaixo:

``` ruby Atribuição
x, y, z = 1, * [2,3] # mesmo que x, y, z = 1,2,3
```

No Ruby 1.8, um splat só pode aparecer antes do último `rvalue` em uma atribuição. No Ruby 1.9, a lista de `rvalues` ​​em uma atribuição paralela pode ter qualquer número de `splats`, e eles podem aparecer em qualquer posição na lista. Não é legal, porém, em qualquer versão da linguagem, para tentar um "splat double" em uma lista aninhada:

```ruby Erro de Atribuição
x,y = **[[1,2]]   # SyntaxError!
```

Os `rvalues` com i​ntervalo de `arrays` e de `hash` pode ser `splatted`. Em geral, qualquer `rvalue` que define um método `to_a` pode ser prefixado com um `splat`. Qualquer objeto <a href="http://ruby-doc.org/core-1.9.3/Enumerable.html">`Enumerable`</a>, incluindo enumeradores pode ser `splatted`, por exemplo. Quando um `splat` é aplicado a um objecto que não define um método `to_a`, nenhuma expansão é realizada e o `splat` avaliado como o próprio objeto.

Quando um `lvalue` é precedido por um asterisco(`*`), isso significa que todos ​​os adicionais `rvalues` devem ser colocados em um `array` e atribuído ao `lvalue`. O valor atribuído a esse `lvalue` é sempre um `array`, e ele pode ter zero, um ou mais elementos:

``` ruby Atribuição
x,*y = 1, 2, 3  # x=1; y=[2,3]
x,*y = 1, 2     # x=1; y=[2]
x,*y = 1        # x=1; y=[]
```

No Ruby 1.8, um `splat` só pode preceder o `lvalue` passado na lista. No Ruby 1.9, do lado esquerdo de uma atribuição paralela pode incluem um operador `splat`, mas pode aparecer em qualquer posição na lista:

``` ruby Atribuição
# Ruby 1.9 somente
*x,y = 1, 2, 3  # x=[1,2]; y=3
*x,y = 1, 2     # x=[1]; y=2
*x,y = 1        # x=[]; y=1
```

Note-se que `splats` pode aparecer em ambos os lados de uma expressão paralela de atribuição:

```ruby Atribuição
x, y, *z = 1, *[2,3,4]  # x=1; y=2; z=[3,4].
```

Finalmente, lembre-se que anteriormente descrevemos dois casos simples de atribuição paralela em que há um `lvalue` único ou uma única `rvalue`. Note-se que ambos os casos se comportam como se existe um `splat` antes da única `lvalue` ou `rvalue`. Explicitamente, incluindo um `splat` em nestes casos não tem qualquer efeito adicional.


<h5>Parênteses na atribuição paralelo</h5>

Uma das características menos compreendidas da atribuição paralela é que o lado esquerdo pode usar parênteses para "sub atrobuição". Se um grupo de dois ou mais `lvalues` ​​é colocado entre parênteses, então é inicialmente tratada como um `lvalue` único. Uma vez que o `rvalue` correspondente foi determinado, as regras de atribuição paralela são aplicadas recursivamente, o `rvalue` que é atribuído ao grupo de `lvalues` ​​que foi em parênteses. Considere o seguinte exercício:

```ruby Atribuição
x, (y, z) = a, b
```

Este é efetivamente executam dois trabalhos ao mesmo tempo:

```ruby Atribuição
x = a
y,z = b
```

Mas note que a segunda tarefa é em si uma atribuição paralela. Como usamos parênteses no lado esquerdo, uma atribuição paralela recursiva é executada. Em ordem para que ele funcione, b deve ser um objeto `splattable` como um `array` ou `enumerador`.

Aqui estão alguns exemplos concretos que devem tornar isso mais claro. Note que os parênteses no ato da esquerda a "desembrulhar" um nível de `array` alinhado à direita:

```ruby Atribuição
x,y,z = 1,[2,3]             # Sem parenteses: x=1;y=[2,3];z=nil
x,(y,z) = 1,[2,3]           # Parenteses: x=1;y=2;z=3

a,b,c,d = [1,[2,[3,4]]]     # Sem parenteses: a=1;b=[2,[3,4]];c=d=nil
a,(b,(c,d)) = [1,[2,[3,4]]] # Parenteses: a=1;b=2;c=3;d=4
```


É isso aí amigos..

Bons estudos e até a proxima! :D