---
layout: post
title: "Threads e Concorrência - Escalando Threads - Part III - #Ruby 1.9"
date: 2012-10-28 15:04
comments: true
categories:
- Thread
- String
- Ruby
- Hash
- Ruby 1.9
- The Ruby Programming Language
---
<!--more-->
Hoje vamos continuar falando de [Ruby](http://www.ruby-doc.org/core-1.9.3/), é hora de nos aprofundar em um pouco de **Threads e Concorrência** agora **Escalando Threads**...

## Threads e Concorrência

### Escalando Threads

Intérpretes do Ruby(irb), muitas vezes têm mais `threads` para executar do que há CPU tem disponível para executá-los. Quando
o processamento paralelo verdadeiro não é possível, é simulado através da partilha de uma CPU entre `threads`. O processo para
a partilha de uma CPU entre as `threads` é chamado de escalonamento de `threads`. Dependendo da implementação e plataforma,
agendamento de `threads` pode ser feito pelo Intérprete do Ruby(irb), ou pode ser tratado pelo sistema operacional.

#### Prioridades da Thread

O primeiro fator que afeta o agendamento de `threads` é prioridades de `thread`: com a alta prioridade da `thread` são
agendadas antes de baixa prioridade de `thread`. Mais precisamente, uma `thread` só vai ficar o tempo de CPU, se não houver
maior prioridade de `thread` aguardando para ser executada.

Definir e consultar a prioridade de um objeto Ruby `Thread` com `priority=` e `priority`. Note-se que não há nenhuma maneira
de definir a prioridade de uma `thread` antes que ela comece a funcionar. Uma `thread` pode, no entanto, aumentar ou diminuir
sua própria prioridade como a primeira ação que toma.

Uma `thread` recém-criada começa com a mesma prioridade que a `thread` que a criou. A `thread` principal começa na prioridade
 0.

Como muitos aspectos de `threading`, prioridades de `threads` são depende da implementação do `Ruby` e do subjacente sistema
operacional. No Linux, por exemplo, `threads` não privilegiadas não pode ter as suas prioridades levantada ou abaixada. Assim,
no Ruby 1.9 (que usa `threads` nativas) no `Linux`, a definição de prioridades de `Thread` é ignorada.

#### Aquisição de Thread e Thread.pass

Quando várias `Threads` com a mesma prioridade precisam compartilhar a CPU, cabe a `thread` programada para decidir quando e
por quanto tempo, cada `thread` é executada. Alguns escalonadores são antecipadas, o que significa que elas permitem a `thread`
a ser executada apenas por um determinado período de tempo antes de permitir outra `thread` da mesma prioridade para ser
executada. Outros programadores não são preempção: uma vez que uma `thread` começa a correr, ela continua funcionando a menos
que durma, blocos para I/O, ou uma `thread` de maior prioridade acorda.

Se uma longa linha de computação liga (ou seja, aquela que nunca faz bloqueio para I/O) está em execução em um agendador
não preemptivo, ela vai "morrer de fome" as outras `threads` com a mesma prioridade, e elas nunca tem a chance de correr.
Para evitar esse problema, de longa duração `compute-bound` `threads` devem chamar periodicamente `Thread.pass` para pedir o
programador para produzir a CPU para outra `thread`.

Até a proxima amigos! :D