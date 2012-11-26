---
layout: post
title: "Threads e Concorrência - Exemplos de Threads - Part IV - #Ruby 1.9"
date: 2012-11-04 09:20
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

Hoje vamos continuar falando de [Ruby](http://www.ruby-doc.org/core-1.9.3/), é hora de nos aprofundar em um pouco de **Threads e Concorrência** agora **Exemplos de Threads**...

#### Exemplos de Threads

Agora que já passamos alguns post falando do modelo `Thread` e da API de `Thread` em Ruby, vamos dar uma olhada em alguns
exemplos reais de vários códigos de `threads`.
<!--more-->
### Leitura de arquivos simultaneamente

O uso mais comum de `threads` de Ruby é em programas que são IO. Eles permitem que os programas mantenham ocupado até 
mesmo enquanto espera por alguma entrada do usuário, o sistema de arquivos, ou da rede. A seguir de código, por exemplo,
define um método `conread` (para leitura simultânea) que leva uma série de nomes de arquivos e retorna um mapa de `hash` 
com esses nomes para o conteúdo desses arquivos. Ele usa `thread` para ler esses arquivos ao mesmo tempo, e é realmente 
destinado a ser utilizado com o módulo `open-uri`, que permite que as URL's `HTTP` e `FTP` possam ser abertas com 
`Kernel.open` e ler como se fossem arquivos:

```ruby Lendo arquivos

# Ler arquivos simultaneamente. Use com o módulo "open-uri" para buscar URLs.
# Passe uma matriz de nomes de arquivos. Retorna um mapa de nomes de arquivos de hash para o conteúdo.
def conread(filenames)
  h = {} 			# hash vazio de resultados

  # Crie uma linha para cada arquivo
  filenames.each do |filename|      # Para cada arquivo chamado
    h[filename] = Thread.new do     # Criar um fio, mapa para filename
      open(filename) {|f| f.read }  # Abra e leia o arquivo
    end 							# valor da linha é o conteúdo do arquivo
  end

  # Percorre o hash, à espera de cada thread para completar.
  # Substitua a thread no hash com o seu valor (o conteúdo de arquivo)
  h.each_pair do |filename, thread| 
    begin
      h[filename] = thread.value    # Mapa de nomes ao conteúdo do arquivo
    rescue
      h[filename] = $!              # Ou a exceção levantada
    end
  end
end
```

### Servidor A Multithreads

Outra, quase canônico caso, o uso de `threads` é para escrever servidores que podem comunicar com mais do que um cliente
de cada vez. Vimos como fazer isto utilizando multiplexagem com Kernel.select, mas um pouco mais simples (Embora 
possivelmente menos escalável) solução usa `threads`:

```ruby Servidor a Multithreads
require 'socket'

# Este método espera um socket ligado a um cliente.
# Ele lê as linhas do cliente, inverte-los e envia-los de volta.
# Múltiplas Threads podem executar este método, ao mesmo tempo.
def handle_client(c)
  while true
    input = c.gets.chop     # Ler uma linha de entrada do cliente
    break if !input         # sai se tem muitas entradas
    break if input=="quit"  # ou se o cliente pede
    c.puts(input.reverse)   # Caso contrário, responde ao cliente.
    c.flush                 # Força a saída para fora
  end
  c.close                   # Fecha o socket cliente
end

server = TCPServer.open(2000) # Ouve na porta 2000

while true                    # Laço de servidores para sempre
  client = server.accept      # Espere um cliente para conectar
  Thread.start(client) do |c| # Inicia uma nova thread
    handle_client(c)          # E Lida com o clinete nessa Thread
  end
end
```

### Iteradores simultâneas

Embora tarefas IO são o caso de uso típico para `threads` de Ruby, eles não se restringem aos que usam. O código a seguir 
adiciona um método `conmap` (por mapa concorrente) para o modulo `Enumerável`. Ele funciona como mapa, mas processa cada
elemento da matriz de entrada com uma distinta `Thread`:

```ruby Iterador Simultâneo
module Enumerable           # Abre o módulo Enumerable
  def conmap(&block)        # Define um novo método que espera um block
    threads = []            # Começa com uma matriz vazia de threads
    self.each do |item|     # Para cada item enumerable
      # Chama o bloco em uma nova Thread, e lembra da Thread
      threads << Thread.new { block.call(item) }
    end
    # Agora mapea o conjunto de Threads para os seus valores
    threads.map {|t| t.value } # E retorna a matriz de valores
  end
end
```

E aqui está uma versão concorrente similar do iterador de cada um:

```ruby Módulo Enumerable
module Enumerable
  def concurrently
    map {|item| Thread.new { yield item }}.each {|t| t.join }
  end
end
```

O código é sucinto e desafiador: se você pode fazer sentido, você está bem em seu caminho para o domínio da sintaxe de 
Ruby e iteradores Ruby.

Lembre-se que no Ruby 1.9, iteradores padrões que não são passado um bloco retorna um objeto enumerador. Isto significa 
que, dado o método `concurrently` definido mais cedo e um objeto `Hash h`, podemos escrever:

``` ruby Método Concurrently
h.each_pair.concurrently {|*pair| process(pair)}
```

Até o proximo amigos!