---
layout: post
title: "Threads e Concorrência - Part I - #Ruby 1.9"
date: 2012-10-28 14:26
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
Hoje vamos continuar falando de [Ruby](http://www.ruby-doc.org/core-1.9.3/), é hora de nos aprofundar em um pouco de **Threads e Concorrência** agora **Thread Lifecycle**...

## Threads e Concorrência

Programas tradicionais têm uma única "thread de execução": as declarações ou instruções que compõem o programa são executadas
sequencialmente até que o programa termina. Um programa multithread tem mais de uma `thread` de execução. Dentro de cada `
thread`, os comandos são executados sequencialmente, mas as próprias `threads` podem ser executadas em paralelo em uma
CPU multicore, por exemplo. Frequentemente (em um núcleo único, uma única CPU, por exemplo), várias `Threads` não são na
realidade executadas em paralelo, mas o paralelismo é simulada intercalando a execução das `threads`.

Programas como o software de processamento de imagem que fazem um monte de cálculos estão a ser dito `compute-bound`. Eles só
podem beneficiar de multithreading, se há realmente múltiplas CPUs para executar os cálculos em paralelo. A maioria dos
programas não são totalmente vinculados a computação, no entanto. Muitos, como navegadores web, passam a maior parte de seu
tempo de espera para a rede ou arquivo `I/O`. Programas como estes estão a ser dito `IO-bound`. Programas `IO-bound` pode ser
útil mesmo quando várias `threads` há apenas uma única CPU disponível. Um navegador pode tornar uma imagem em uma `thread`
enquanto outra `thread` está à espera para a próxima imagem para ser baixada da rede.

Ruby faz com que seja fácil de escrever programas `multi-threaded` com a `Class Thread`. Para iniciar uma nova `thread`,
apenas associa um bloco com uma chamada para `Thread.new`. Uma nova `thread` será criada para executar o código no bloco, e a
`thread` original retornará do `Thread.new` imediatamente e continuar a execução com a afirmação seguinte:

```ruby Iniciando um thread
# Thread #1 está sendo executado aqui
Thread.new {
  # Thread #2 executa esse código
}
# Thread #1 executa esse código
```

Vamos começar nossa cobertura de `threads` explicando o modelo `Thread` de Ruby e API em alguns detalhes. Estas seções
introdutórias explicam as coisas como ciclo de vida da `thread`, agendamento de threads, e os estados da `thread`. Com que o
material introdutório como pré-requisito, passamos a apresentar código de exemplo e para cobrir `threads` avançadas como
sincronização de `threads`.

Finalmente, é importante notar que os programas de Ruby também pode alcançar simultaneidade ao nível do processo de sistema
operacional executando executáveis externos ​​ou novas cópias de bifurcação do interpretador Ruby. Fazendo isto é dependem do
sistema operacional. Para mais informações, use `ri` para procurar os métodos `Kernel.system`, `Kernel.exec`, `Kernel.fork`,
`IO.popen`, e o módulo `Process`.

### Lifecycle Tópico

Como descrito acima, novas `threads` são criados com `Thread.new`. Você também pode usar os sinónimos `Thread.start` e
`Thread.fork`. Não há necessidade de se iniciar uma `thread` depois de criá-la, ele começa a ser executado automaticamente
quando os recursos da CPU estejam disponíveis. O valor da invocação `Thread.new` é um objeto `Thread`. A classe `Thread`
define um número de métodos para consultar e manipular a `thread` enquanto ela está sendo executada.

Uma `thread` é executa o código do bloco associado à chamada para `Thread.new` e depois pára execução. O valor da última
expressão em que o bloco é o valor da `thread`, e pode ser obtido chamando o método do valor do objeto `Thread`. Se a `thread`
foi executado para conclusão, então o valor retorna o valor da `thread` de imediato. Caso contrário, os blocos de valor do
método e não retorna até que a `threado` for concluída.

O método de classe `Thread.current` retorna o objeto `Thread` que representa o atual `thread`. Isso permite que as `threads`
manipulam-se. O método da classe `Thread.main` retorna o objeto `Thread` que representa a principal `thread`, este é a
`thread` inicial de execução que começou quando o Programa Ruby foi iniciado.

#### A Thread principal

A `Thread` principal é especial: o interpretador Ruby pára de correr quando a `thread` principal é feita. Ele faz isso mesmo
que a `thread` principal criou outras `threads` que ainda estão em execução. Você deve garantir, portanto, que a sua princial
`thread` não termina enquanto outras `threads` ainda estão em execução. Uma maneira de fazer isso é escrever sua `thread`
principal sob a forma de um `loop` infinito. Outra maneira é explicitamente esperar para as `threads` ser concluída. Já
mencionamos que você pode chamar o método `value` de uma `thread` que espera que ela termine. Se você não se importa com o
valor de suas `threads`, você pode esperar com o método de instancia `join`.

O seguinte método espera até que todas as linhas, com excepção da `thread` principal e a `thread` atual (que pode ser a mesma
coisa), ter saído:

``` ruby Thread Principal
def join_all
  main = Thread.main        # Thread Principal
  current = Thread.current  # Thread atual
  all = Thread.list         # Todas as threads ainda em execução
  # Agora chama join em cada thread
  all.each {|t| t.join unless t == current or t == main }
end
```

#### Threads e exceções não tratadas

Se uma exceção é levantada na `thread` principal, e não é tratada em qualquer lugar, o interpretador Ruby imprime uma
mensagem e sai. Em outras `threads` que a `thread` principal, exceções não tratadas causam a `thread` para parar de executar.
Por defeito, no entanto, isto não faz o intérprete para imprimir uma mensagem ou saída. Se uma `thread` `t` sai por causa de
uma exceção não tratada, e outra `thread` de chamadas `t.join` ou `t.value`, então a exceção que ocorreu em `t` é levantada
na `thread` de `s`.

Se você gostaria de qualquer exceção não tratada em qualquer `thread` para fazer com que o intérprete saia, use o método de
classe ` Thread.abort_on_exception=`:

```ruby Thread
Thread.abort_on_exception = true
```

Se você quer uma exceção não tratada em uma `thread` específica faz com que o intérprete saia, utilizando o método de
exemplo, através do mesmo nome:

```ruby Thread abort
t = Thread.new { ... }
t.abort_on_exception = true
```

É isso ai amigos.. até a proxima!