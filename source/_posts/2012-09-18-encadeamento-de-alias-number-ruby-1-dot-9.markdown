---
layout: post
title: "Encadeamento de Alias - #Ruby 1.9"
date: 2012-09-18 08:38
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
<b>Reflexão e Metaprogramação</b> agora <b>Encadeamento de Alias</b>...</p>

<h1>Encadeamento de Alias</h1>

Como já visto, metaprogramação em Ruby muitas vezes envolve a dinâmica definição de métodos. Assim como comum é a dinâmica modificação de métodos.
Métodos são modificados com uma técnica que chamaremos de encadeamento de alias. Ele funciona assim:

	* Primeiro, criar um alias para o método a ser modificado. este apelido fornece um nome para 
	a versão não modificada do método.

	* Em seguida, definem uma nova versão do método. Esta nova versão deve chamar a versão não modificada
	através dos alias, mas pode adicionar qualquer funcionalidade que for necessário, antes e depois de que
	faz isso.

Note-se que estes passos podem ser aplicados repetidamente (desde que um alias diferente é usado de cada vez), criando uma cadeia de métodos e aliases.

Este post inclui três exemplos de encadeamento de alias. O primeiro realiza o encadeamento de apelido estaticamente, ou seja, usando pseudônimo
regulares e declarações `def`. Os segundo e terceiro exemplos são mais dinâmicos; eles são apelidos que acorrentam métodos arbitrariamente nomeados
utilizando `alias_method`, `define_method` e `class_eval`.

<!--more-->

<h3>Rastreando Arquivos Carregados e Classes Definidas</h3>

O `Exemplo 1-1` é um código que mantém o controle de todos os ficheiros carregados e todas as classes definidas num programa. Quando o programa sai,
ele imprime um relatório. Você pode usar este código para "instrumento" de um existente programa para que você entenda melhor o que está fazendo. Uma
maneira de usar este código é inserir esta linha no começo do programa:

``` ruby classtrace
require 'classtrace'
```

Uma solução mais fácil, no entanto, é usar a opção -r para o seu intérprete Ruby(`irb`):

``` ruby Opção -r
ruby -rclasstrace my_program.rb  --traceout /tmp/trace
```

A opção -r carrega a biblioteca especificado antes de começar a executar o programa.

O `Exemplo 1-1` usa apelido de encadeamento estático para rastrear todas as chamadas dos métodos `Kernel.require` e `Kernel.load`. Ele define um hook
`Object.inherited` para rastrear as definições de novas classes. E ele usa `Kernel.at_exit` para executar um bloco de código quando o programa termina. 
Além dos encadeamentos de alias `require` e `load` e defini `Object.inherited`, a única modificação do espaço global feita por este código é a 
definição de um módulo chamado `ClassTrace`. Todo o estado necessário para o rastreio é armazenado em constantes dentro deste módulo, de modo que não
poluem o `namespace` com variáveis globais.

``` ruby Exemplo 1-1. Rastreando Arquivos Carregados e Classes Definidas
# Definimos este módulo para manter o estado global do require, de modo que
# Nós não alteramos o espaço global mais do que o necessário.
module ClassTrace
   # Esta matriz mantém a nossa lista de arquivos carregados e classes definidas.
   # Cada elemento é um subarray segurando a classe definida ou o
   # Arquivo carregado e o quadro de pilha onde ele foi definido ou carregado.
   T = []  # Array para armazenar os arquivos carregados

   # Agora defini a constante OUT para especificar onde saída do rastreamento vai.
   # O padrão é stderr, mas também pode vir a partir de argumentos na linha de comando
   if x = ARGV.index("--traceout")    # Se existe argumento
     OUT = File.open(ARGV[x+1], "w")  # Abre o arquivo especificado
     ARGV[x,2] = nil                  # E remova os argumentos
   else
     OUT = STDERR                     # Caso contrário, o padrão para STDERR
   end
end

# Passo 1 encadeamento Alias: definir aliases para os métodos originais
alias original_require require
alias original_load load

# Passo 2 encadeamento Alias 2: definir novas versões dos métodos
def require(file)
  ClassTrace::T << [file,caller[0]]     # Lembre-se de onde que estava carregado
  original_require(file)                # Chame o método original
end

def load(*args)
  ClassTrace::T << [args[0],caller[0]]  # Lembre-se de onde que estava carregado
  original_load(*args)                  # Chame o método original
end

# Este método hook é chamado de cada vez que uma nova classe é definida
def Object.inherited(c)
  ClassTrace::T << [c,caller[0]]        # Lembre-se onde que foi definido
end

# Kernel.at_exit registra um bloco a ser executado quando o programa sai
# Vamos utilizá-lo para comunicar os dados de arquivo e de classe que recolhemos
at_exit {
  o = ClassTrace::OUT
  o.puts "="*60
  o.puts "Files Loaded and Classes Defined:"
  o.puts "="*60
  ClassTrace::T.each do |what,where|
    if what.is_a? Class  # Report class (with hierarchy) defined
      o.puts "Defined: #{what.ancestors.join('<-')} at #{where}"
    else                 # Report file loaded
      o.puts "Loaded: #{what} at #{where}"
    end
  end
}
```

<h3>Métodos encadeamento de segurança da Thread</h3>

O alias de encadeamento é feito pelo método `Module.synchronize_method`, o qual, por sua vez usa um método auxiliar `Module.create_alias` para definir
um alias adequado para qualquer método dado (incluindo métodos como o operador +).

Depois de definir estes novo métodos `Module`, Exemplo 1-2 redefine o método `synchronized` novamente. Quando o método é invocado dentro de uma classe
ou de um módulo, ele chama `synchronize_method` em cada um dos símbolos que é passado. Curiosamente, contudo, pode também ser chamado sem argumentos,
quando utilizado desta forma, acrescenta sincronização para qualquer método de instância é definido a seguir. (Utiliza o `hook` para receber 
notificação quando um novo método `method_added` é adicionado.) Note que o código deste exemplo depende do método `Object.mutex` e a classe 
`SynchronizedObject`.

``` ruby Exemplo 1-2. Alias de encadeamento de segurança da Thread
# Define um alias corrente Module.synchronize_method de métodos de instância
# Assim que sincronizar a instância antes da execução.
class Module
  # Esta é uma função auxiliar para o encadeamento alias.
  # Dado o nome de um método (como uma string ou símbolo) e um prefixo, cria
  # Um alias exclusivo para o método, e retornar o nome do alias
  # Como um símbolo. Quaisquer caracteres de pontuação em nome método original
  # Serão convertidos em números para que os operadores podem ser alias.
  def create_alias(original, prefix="alias")
    # Cole o prefixo do nome original e converter pontuação
    aka = "#{prefix}_#{original}"
    aka.gsub!(/([\=\|\&\+\-\*\/\^\!\?\~\%\<\>\[\]])/) {
      num = $1[0]                       # Ruby 1.8 character -> ordinal
      num = num.ord if num.is_a? String # Ruby 1.9 character -> ordinal
      '_' + num.to_s
    }
    
    # Mantenha acrescentando ressalta até chegarmos a um nome que não está em uso
    aka += "_" while method_defined? aka or private_method_defined? aka

    aka = aka.to_sym           # Converter o nome de alias de um símbolo
    alias_method aka, original # Na verdade criar o alias
    aka 											 # Retorna o nome do alias
  end

  # Alias correntam o método nomeado para adicionar sincronização
  def synchronize_method(m)
    # Primeiro, fazemos um alias para a versão dessincronizado do método.
    aka = create_alias(m, "unsync")
    # Agora redefini o original para invocar o alias em um bloco sincronizado.
    # Queremos o método definido como sendo capaz de aceitar os blocos, de modo que
    # Não pode usar define_method, e deve avaliar vez uma string com
    # Class_eval. Note-se que tudo entre% Q {} e da correspondência
    # É uma string entre aspas, e não um bloco.
    class_eval %Q{
      def #{m}(*args, &block)
        synchronized(self) { #{aka}(*args, &block) }
      end
    }
  end
end

# Este método global sincronizado agora pode ser usado de três maneiras diferentes.
def synchronized(*args)
  # Caso 1: com um argumento e um bloco, sincronizar sobre o objeto
  # E executar o bloco
	if args.size == 1 && block_given?
    args[0].mutex.synchronize { yield }

  # Caso dois: com um argumento que não é um símbolo e nenhum bloco
  # Devolve um invólucro de SynchronizedObject
  elsif args.size == 1 and not args[0].is_a? Symbol and not block_given?
    SynchronizedObject.new(args[0])

  # Caso três: quando invocado em um módulo com nenhum bloco, alias a cadeia
  # Chamado métodos para adicionar sincronização. Ou, se não há argumentos,
  # Então apelido acorrentam o próximo método definido.
  elsif self.is_a? Module and not block_given?
    if (args.size > 0) # Synchronize the named methods
      args.each {|m| self.synchronize_method(m) }
    else
      # Se nenhum método é especificado pelo synchronize o método seguinte define
      eigenclass = class<<self; self; end 
      eigenclass.class_eval do # Use eigenclass para definir métodos de classe
        # Define method_added para notificação quando próximo método é definido
        define_method :method_added do |name|
          # Primeiro remover esse método hook
          eigenclass.class_eval { remove_method :method_added }
          # Em seguida, sincronize o método que acabou de ser adicionado
          self.synchronize_method name
        end
      end
    end

  # Caso 4: qualquer outra invocação é um erro
  else
    raise ArgumentError, "Invalid arguments to synchronize()"
  end
end
```

<h3>Métodos de encadeamento para Rastreamento</h3>

O Exemplo 1-3 suporta o rastreio de métodos denominados de um objeto. Ele define `trace!` e `untrace!` a cadeia e desencadeiam métodos chamados de um
objeto.

A coisa interessante sobre esse exemplo é que ele faz o seu encadeamento de um modo diferente a partir do Exemplo 1-2. Ele simplesmente define métodos
únicos no objeto e usa `super` dentro do `singleton` para a cadeia de definição do método original de exemplo. Nenhum método são criado aliases.

``` ruby Exemplo 8-10. Encadeamento com métodos singleton para rastrear
# Define métodos trace! e untrace! de instância para todos os objetos.
# trace! "Cadeias" os métodos chamados por definir métodos singleton
# Que adiciona a funcionalidade de rastreamento e use super para chamar o original.
# untrace! exclui os métodos singleton para remover o rastreamento.
classe Object
  # os métodos trace especificados, enviando a saída para STDERR.
  def trace!(*methods)
    @_traced = @_traced || []    # Lembre-se o conjunto de métodos traçados

    # Se nenhum método foi especificado, use todos os métodos públicos definidos
    # Diretamente (não herdado) pela classe deste objeto
    methods = public_methods(false) if methods.size == 0

    methods.map! {|m| m.to_sym }	# Converta qualquer cordas para símbolos
    methods -= @_traced 					# remove métodos que já estão traçadas
    return if methods.empty?     	# Voltar mais cedo se não há nada a fazer
    @_traced |= methods           # Adiciona métodos para definir métodos de traçados

    # Trace o fato de que estamos começando a traçar estes métodos
    STDERR << "Tracing #{methods.join(', ')} on #{object_id}\n"
	
		# Singleton métodos são definidos na eigenclass
    eigenclass = class << self; self; end

		methods.each do |m| # Para cada método m
      # Define uma versão trace singleton do método m.
      # Saída de informações de rastreamento e usar super para invocar o
      # Método de instância que é o rastreamento.
      # Queremos que os métodos definidos para ser capaz de aceitar blocos, de modo que
      # Não pode usar define_method, e deve avaliar, em vez de uma string.
      # Note que tudo entre %Q{} e a correspondência é uma
      # Entre aspas de string, não um bloco. Observe também que há
      # Dois níveis de interpolações de string aqui. # {} É interpolada
      # Quando o método singleton é definida. E \ # {} é interpolada
      # Quando o método singleton é invocado.
      eigenclass.class_eval %Q{
        def #{m}(*args, &block)
          begin
            STDERR << "Entering: #{m}(\#{args.join(', ')})\n"
            result = super
            STDERR << "Exiting: #{m} with \#{result}\n"
            result
          rescue
            STDERR << "Aborting: #{m}: \#{$!.class}: \#{$!.message}"
            raise
          end
        end
      }
    end
  end

  # Untrace os métodos especificados ou todos os métodos rastreados
	def untrace!(*methods)
    if methods.size == 0    # Se nenhuma métodos especificados untrace
      methods = @_traced    # todos os métodos atualmente rastreados
      STDERR << "Untracing all methods on #{object_id}\n"
    else                    # Caso contrário, untrace
      methods.map! {|m| m.to_sym }  # Converter string para símbolos
      methods &= @_traced   # todos os métodos especificados que são rastreados
      STDERR << "Untracing #{methods.join(', ')} on #{object_id}\n"
    end
		
		@_traced -= methods     # Retire-os do nosso conjunto de métodos de traçados

		# Remove os métodos traçados únicos do eigenclass
		# Note que nós class_eval um bloco aqui, não uma string
		(class << self; self; end).class_eval do
		  methods.each do |m|
		    remove_method m     # undef_method não funciona corretamente
		  end
		end

		# Se nenhum método são traçados mais, remover o nosso exemplo var
		if @_traced.empty?
		  remove_instance_variable :@_traced
		end
  end
end
```

É isso ai galera! Até a proxima!