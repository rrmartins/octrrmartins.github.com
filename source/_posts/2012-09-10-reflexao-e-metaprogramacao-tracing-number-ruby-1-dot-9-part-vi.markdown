---
layout: post
title: "Reflexão e Metaprogramação - Tracing - #Ruby 1.9 - Part VI"
date: 2012-09-10 22:50
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
<b>Reflexão e Metaprogramação</b> agora <b>Tracing</b>... Estranho para alguns, mas, veremos que é simples!</p>

<h1>Tracing</h1>

Ruby define uma série de características para rastrear a execução de um programa. Estes principalmente são os úteis para a depuração do código e
imprimir mensagens de erro informativos. Duas das mais simples das características são as palavras-chave da linguagem: `__FILE__` e `__LINE__`.
Essas palavras-chave sempre avaliam o nome do arquivo e o número da linha dentro do arquivo em que se aparece, e eles permitem que uma mensagem de erro
para especificar o local exato em que ela foi gerado:

``` ruby __FILE__ e __LINE__
STDERR.puts "#{__FILE__}:#{__LINE__): invalid data"
```
<!--more-->
Como um aparte, note que os métodos `Kernel.eval`, `Object.instance_eval`, e `Module.class_eval` todos aceitam um nome de arquivo (ou outra seqüência) e
um número de linha como os seus dois últimos argumentos. Se você está avaliando o código que você tenha extraído de um arquivo de algum tipo, você pode
usar esses argumentos para especificar os valores de `__FILE__` e `__LINE__` para a avaliação.

Você, sem dúvida, notou que quando uma exceção não tratada, a mensagem de erro impressa no console contém nome e informações de número de linha. Esta
informação é baseada em __FILE__ e __LINE__, é claro. Cada Objeto de exceção tem um backtrace associado a ele que mostra exatamente onde ele foi criado,
onde o método que levantou a exceção foi invocado, onde esse método foi chamado, e assim por diante. O método `Exception.backtrace` retorna um `array` de
`strings` contendo essa informação. O primeiro elemento do `array` é este o local em que ocorreu a excepção, e cada elemento subsequente é um quadro de
pilha maior.

Você não precisa levantar uma exceção para obter um rastreamento da pilha atual, no entanto. O método `Kernel.caller` retorna o estadp atual na pilha de
chamadas da mesma forma como `Exception.backtrace`. Com nenhum argumento, o `caller` retorna um rastreamento de pilha, cujo primeiro elemento é o método
que chamou o método que chama de `caller`. Isto é, `caller[0]` especifica o local a partir do qual o método atual foi chamado. Você também pode chamar
de `caller` com um argumento que especifica quantos quadros de pilha a cair a partir do início do registo de `caller`. O padrão é `1`, e do 
`caller(0)[0]` especifica o local em que o método `caller` é invocado. Isto significa, por exemplo, que o `caller[0]` é a mesma coisa que o 
`caller(0)[1]` e que o `caller(2)` é o mesmo como `caller[1 .. -1]`.

Rastreamentos de pilha devolvidos por `Exception.backtrace` e `Kernel.caller` também incluem nomes de métodos. Antes de Ruby 1.9, você deve analisar as
seqüências de rastreamento de pilha para extrair nomes de métodos. No Ruby 1.9, no entanto, você pode obter o nome (como um símbolo) da execução 
atual do método com `Kernel.__method__` ou seu sinônimo `Kernel.__callee__`. `__method__` é útil em conjunção com __FILE__ e __LINE__:

``` ruby raise
raise "Assertion failed in #{__method__} at #{__FILE__}:#{__LINE__}"
```

Note-se que `__method__` retorna o nome pelo qual um método foi originalmente definido, mesmo que o método foi invocado por um alias.

Em vez de simplesmente imprimi o nome e número em que ocorre um erro, você pode dar um passo adiante e mostrar a linha real de código. Se o seu programa
define uma constante global `SCRIPT_LINES__` e define-a igual a um `hash`, então os requisitos e métodos de carregar, adicionar uma entrada para este
`hash` para cada arquivo que carregar. As chaves de hash são nomes de arquivos e os valores associados com essas chaves são `arrays` que contêm as
linhas destes arquivos. Se você quiser incluir o arquivo principal (em vez que apenas incluir os arquivos que ele necessita), no `hash`, inicializá-lo
assim:

``` ruby SCRIPT_LINES__
SCRIPT_LINES__ = {__FILE__ => File.readlines(__FILE__)}
```

Se você fizer isso, então você pode obter a linha atual de código-fonte em qualquer lugar em seu programa com esta expressão:

``` ruby SCRIPT_LINES__
SCRIPT_LINES__[__FILE__][__LINE__-1]
```

Ruby permite rastrear atribuições para variáveis globais com `Kernel.trace_var`. Passe este método um símbolo que dá nome a uma variável global e uma
`string` ou bloco de código. Quando o valor das alterações de nomes de variáveis, a cadeia de caracteres será avaliada ou o bloco será invocado. Quando
um bloco é especificado, o novo valor da variável é passado como um argumento. Para parar o rastreamento da variável, chame `Kernel.untrace_var`. No
seguinte exemplo, notar o uso de `caller[1]`, para determinar o local do programa em que o bloco de rastreamento da variável foi invocado:

``` ruby variavies globais
# Imprime a mensagem de cada vez $ mudanças SAFE
trace_var(:$SAFE) {|v|
  puts "$SAFE set to #{v} at #{caller[1]}"
}
```

O último método de rastreamento é `Kernel.set_trace_func`, que registra um `Proc` a ser chamado após cada linha de um programa Ruby. `set_trace_func` 
é útil se você quer escrever um módulo depurador que permite que linha por linha de passo através de um programa, mas não vamos cobri-lo em detalhes aqui.


Até a proxima amigos... =D