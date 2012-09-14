---
layout: post
title: "Estruturas de Controle Personalizados - #Ruby 1.9"
date: 2012-09-13 22:50
comments: true
categories: 
- Ruby API
- Integer
- String
- Object
- Kernel
- Ruby
- Hook
- Ruby 1.9
- The Ruby Programming Language
---

<p>Hoje vamos continuar falando de <a href="http://www.ruby-doc.org/core-1.9.2/">Ruby</a>, é hora de continuar nos aprofundando um pouco mais de
<b>Reflexão e Metaprogramação</b> agora <b>Estruturas de Controle Personalizados</b>... Estranho para alguns, mas, veremos que é simples!</p>

<h1>Estruturas de Controle Personalizados</h1>

Uso de blocos em Ruby, juntamente com sua sintaxe de parênteses opcional, tornam muito fácil de definir métodos que parecem `iterator` e se comportam
como estruturas de controle. O método `loop` do `Kernel` é um exemplo simples. Neste post, desenvolvo mais três exemplos. Os exemplos aqui usam
segmentação da API do Ruby, você pode precisar de ler Threads e Concorrência para compreender todos os detalhes.
<!--more-->

<h3>Executando Delaying e Repeating: `after` e `every`</h3>

O exemplo 1-1 define métodos globais nomeados após os dias. Cada um leva um argumento numérico que representa um número de segundos e devem ter um
bloco associado. Depois cria um novo segmento e retorna o objeto `Thread` imediatamente. O segmento recém-criado dorme para o número especificado de
segundos e, então, chama (sem argumentos) o bloco que você forneceu. Tudo é semelhante, mas ele chama o bloco repetidamente, "dorme" o número
especificado de segundos entre chamadas. O segundo argumento para todos é um valor para passar para a primeira chamada do bloco. O valor de retorno de
cada invocação se torna o valor que passou para a próxima invocação. O bloco associado a cada intervalo pode ser usado para prevenir qualquer 
invocações futuras.

Aqui está um exemplo de código que usa `after` e `every`:

``` ruby after e every
require 'afterevery'

1.upto(5) {|i| after i { puts i} }  # Lentamente imprimir os números 1-5
sleep(5)                            # Aguarde cinco segundos
every 1, 6 do |count|               # Agora, lentamente, imprimir 6-10
	puts count
	break if count == 10
  count + 1                         # O valor próximo de contagem
end
sleep(6)                            # Dê um tempo acima para executar
```

Chamando o `sleep` no final deste código, evita o programa de sair antes que a `thread` seja criada por poder todas completar sua contagem. Com esse
exemplo de como `after` e `every` são usadas, agora estamos prontos para apresentar a sua implementação. 

``` ruby Exemplo 1-1. Os métodos after e every

#
# Defini métodos de kernel after e every por adiar blocos de código.
# Exemplos:
#
#   after 1 { puts "done" }
#   every 60 { redraw_clock }
#
# Ambos os métodos retornam objetos Thread. Chame kill sobre os objetos devolvidos
# Para cancelar a execução do código.
#
# Note que essa é uma implementação muito ingênua. A mais robusta
# Implementação usaria uma Thread timer para todas as tarefas globais,
# Permitiria uma maneira de recuperar o valor de um bloco diferido, e iria
# Fornecer uma maneira de esperar por todas as tarefas pendentes para ser concluído.
#

# Executar o bloco after depois de esperar o número especificado de segundos.
def after(seconds, &block)  
  Thread.new do       # Em um novo segmento ...
    sleep(seconds)    # Primeiro espera
    block.call        # Em seguida, chamar o bloco
  end # Retorna o objeto Thread de imediato
end

# Repete sleep e after executando o bloco.
# Passa valor para o bloco na primeira chamada.
# Em chamadas subseqüentes, passar o valor da chamada anterior.
def every(seconds, value=nil, &block)
  Thread.new do                 # Em um novo segmento ...
    loop do                     # loop para sempre (ou até ruptura no bloco)
      sleep(seconds)            # sleep
      value = block.call(value) # E invocar bloco
    end # Em seguida, repita ..
  end # cada retorna o Tópico
end
```

<h3>Thread de segurança com blocos sincronizados</h3>

Ao escrever programas que usam várias `Threads`, é importante que duas `threads` não tente modificar o mesmo objeto, ao mesmo tempo. Uma maneira de
fazer isto é colocar o código que deve ser feito em uma `thread` segura em um bloco associado a uma chamada para o método de `synchronize` de um objeto
`Mutex`. No Exemplo 1-2 que levar isso a um passo adiante, e emula a palavra-chave `synchronized` do Java com um método global chamado `synchronized`.
Este método `synchronized` espera um único objeto como argumento e um bloco. Ele obtém um `Mutex` associado ao objeto, e usa `Mutex.synchronize` para
invocar o bloco. A parte complicada é que o objeto de Ruby, ao contrário de objetos Java, não tem um `Mutex` que lhes estão associados. Então o Exemplo
1-2 também define um método de instância chamado `mutex` em Object. Curiosamente, a implementação deste método `mutex` usa `synchoronized` na sua forma
de palavras-chave novo estilo!

`Mutex` -> <a href="http://www.ruby-doc.org/core-1.9.2/Mutex.html">http://www.ruby-doc.org/core-1.9.2/Mutex.html</a>

``` ruby Exemplo 1-2. Simples blocos sincronizados

require 'thread'

# Ruby 1.8 mantém Mutex nesta biblioteca

# Obter o Mutex associado com o objeto o, e então avalia
# Bloco sob a proteção do Mutex.
# Este funciona como a palavra-chave synchronized do Java.
def synchronized(o)
  o.mutex.synchronize { yield }
end

# Object.mutex na verdade não existe. Temos que definir isso.
# Este método retorna um Mutex único para cada objeto, e
# Sempre retorna o mesmo Mutex para qualquer objeto particular.
# Cria Mutexes lazily, o que requer sincronização para
# Segurança da Thread.
class Object
  # Retorna o Mutex para este objeto, criando, se necessário.
  # A parte difícil é ter certeza de que duas threads não chamam
  # Isso ao mesmo tempo e acabam por criar dois mutexes diferentes.
  def mutex
    # Se este objeto já tem um mutex, basta devolvê-lo
    return @__mutex if @__mutex

    # Caso contrário, nós temos que criar um mutex para o objeto.
    # Para fazer isso com segurança que temos para sincronizar em nosso objeto de classe.
    synchronized(self.class) {
      # Verifique novamente: no momento em que entrar neste bloco sincronizado,
      # Alguma outra thread pode já ter criado o mutex.
      @__mutex = @__mutex || Mutex.new
    }
    # O valor de retorno é @__mutex
  end
end

# O método Object.mutex definido acima, necessita para bloquear a classe
# Se o objeto não tiver um Mutex ainda. Se a classe não tem
# Mutex próprio ainda, então a classe da classe (a Class do Object)
# Será bloqueada. A fim de evitar recursão infinita, devemos
# Garantir que o objeto da classe tem um mutex.
Class.instance_eval { @__mutex = Mutex.new }
```

É isso ai amigos, até o proximo! :)