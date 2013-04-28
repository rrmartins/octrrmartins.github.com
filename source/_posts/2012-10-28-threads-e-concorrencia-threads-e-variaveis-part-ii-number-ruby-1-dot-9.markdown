---
layout: post
title: "Threads e Concorrência - Threads e Variáveis - Part II - #Ruby 1.9"
date: 2012-10-28 14:53
comments: true
categories:
- Ruby API
- Thread
- String
- Ruby
- Hash
- Ruby 1.9
- The Ruby Programming Language
---
<!--more-->
Hoje vamos continuar falando de [Ruby](http://www.ruby-doc.org/core-1.9.3/), é hora de nos aprofundar em um pouco de **Threads e Concorrência** agora **Threads e Variáveis**...

## Threads e Concorrência

### Threads e Variáveis

Uma das características-chave de `Thread` é que elas podem compartilhar o acesso a variáveis. Como `Threads` são definidas
por blocos, eles têm acesso a qualquer que seja variáveis ​​(variáveis ​​locais, variáveis ​​de instância, variáveis ​​globais e
 assim por diante) estão no escopo do bloco:

``` ruby Thread e Variavel
x = 0

t1 = Thread.new do
  # Esta Thread pode consultar e definir a variável x
end

t2 = Thread.new do
  # Esta Thread e também consulta e seta x
  # E pode consultar e definir T1 e T2 também.
end
```

Quando dois ou mais `Thread` de ler e escrever as mesmas variáveis ao mesmo tempo, elas devem tomar cuidado para que elas o
fazem corretamente. Nós vamos ter mais a dizer sobre isso quando consideramos a sincronização de `threads` abaixo.

#### Thread-privadas variáveis

Variáveis ​​definidas dentro do bloco de uma `thread` são particulares para essa `thread` e não são visíveis para qualquer
outra `thread`. Isto é simplesmente consequência de regras de variáveis de escopo ​​Ruby.

Muitas vezes queremos uma `Thread` tenha sua própria cópia privada de uma variável de modo a que o seu comportamento não se
altere se o valor do referido mude de variáveis. Considere o seguinte código, que tenta criar três tópicos que impressão (
respectivamente) os números 1, 2 e 3:

``` ruby Threads Privadas
n = 1
while n <= 3
  Thread.new { puts n }
  n += 1
end
```

Em algumas circunstâncias, em algumas implementações, este código pode funcionar como o esperado e imprimir os números 1, 2 e
3. Em outras circunstâncias, ou em outras implementações, talvez não. É perfeitamente possível (se tópicos recentemente
criados não executa de imediato) para o código imprimir 4, 4, e 4, por exemplo. Cada thread lê uma cópia compartilhada da
variável n, e o valor ds mudanças de variáveis ​​como o loop é executado. O valor impresso pela `thread` depende de quando esse
segmento é executado em relação para a thread pai.

Para resolver este problema, passamos o valor atual de n para o método `Thread.new`, e atribuimos o atual valor da variável a
um parâmetro de bloco. Parâmetros de bloco são privados para o bloco, e este valor particular não é partilhado entre tópicos:

``` ruby Threads Privadas
n = 1
while n <= 3
  # Obtem uma cópia privada do valor atual de n em x
  Thread.new(n) {|x| puts x }
  n += 1
end
```

Note-se que temos uma outra forma de resolver este problema é a utilização de um iterador em vez de um loop `while`. Neste
caso, o valor de `n` é modificado para particular para o bloco externo e nunca durante a execução desse bloco:

```ruby Thread Privada com Interator
1.upto (3) {| n | Thread.new {puts n}}
```


#### Variáveis ​​de Threads locais

Algumas das variáveis especiais globais de Ruby são `thread` local: elas podem ter valores diferentes em `threads` diferentes.
`$SAFE` e `$~` são exemplos. Isto significa que, se dois `thread` estão realizando conconrrencia de expressão regular ao mesmo
tempo, eles vão ver diferentes valores de `$~`, e a realização de um jogo em um fio não irá interferir com os resultados de
um jogo executado na outra discussão.

A classe `Thread` provê `hash-like` como o comportamento. Ele define métodos de instância `[]` e `[]=` que permitem associar
valores arbitrários com qualquer símbolo. (Se você usar uma cadeia de caracteres em vez disso, ele será convertido em um
símbolo. Ao contrário `hashs` de verdade, a classe `Thread` só permite símbolos como chaves.) Os valores associados a estes
símbolos comportam-se como variáveis ​​de `Thread` locais. Eles não são privados como variáveis de block ​​locais porque qualquer
`Thread` pode pesquisar um valor em qualquer outra `Thread`. Mas eles não são variáveis partilhadas, uma vez que cada Thread
pode ter a sua própria cópia.

Como exemplo, suponha que nós criamos `thread` para download de arquivos de um servidor web. A `Thread` principal pode querer
monitorar o progresso do download. Para permitir isso, cada `Thread` pode fazer o seguinte:

```ruby Thread de progresso
Thread.current[:progress] = bytes_received
```

A `Thread` principal poderia, então, determinar o total de bytes baixado com um código como este:

```ruby Thread de progresso
total = 0
download_threads.each {|t| total += t[:progress] }
```

Junto com `[]` e `[]=`, `Thread` também define um método `key?` para testar se uma determinada chave existe para uma discussão
. Os métodos `keys` retorna uma matriz de símbolos que representam as chaves definidas para a `Thread`. Este código pode ser
melhor escrito como se segue, de modo que ela trabalhe de tópicos que ainda não começou a correr e não tenha definido a chave
:progress ainda:

```ruby Thread de progresso
total = 0
download_threads.each {|t| total += t[:progress] if t.key?(:progress)}
```

Até a proxima galera! :D