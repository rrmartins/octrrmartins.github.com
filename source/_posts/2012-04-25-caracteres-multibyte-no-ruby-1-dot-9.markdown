---
layout: post
title: "Caracteres multibyte no Ruby 1.9"
date: 2012-04-25 22:27
comments: true
categories: 
- Ruby
- Ruby 1.8
- Ruby 1.9
- The Ruby Programming Language
---
<p>Continuando os estudos de Ruby, e a leitura do livro The Ruby Programming Language</p>

<p>A classe String foi reescrita no Ruby 1.9 para estar ciente e para
lidar corretamente com caracteres multibyte. Embora o apoio de vários bytes é
a maior mudança no Ruby 1,9, não é uma mudança altamente visível:
código que usa string multibyte simplesmente funciona. Vale a pena
entender por que ele funciona, no entanto, e esta seção explica o
detalhes.</p>
<!-- more -->

<p>Se uma string contém caracteres de vários bytes, então o número de
bytes não corresponde ao número de caracteres. No Ruby 1.9,
o comprimento ea
métodos tamanho retornar o número de
caracteres em uma seqüência, eo método novo tamanho byte
retorna o número de bytes. [] E [] = operadores permitem consultar e definir
os caracteres de uma string, e os novos métodos e getByte setbyte permitem que você
para consultar e definir bytes individuais (embora você não deve muitas vezes precisam
para fazer isso):</p>

``` ruby Encode
# - * - Coding: utf-8 - * - # Especifique Unicode UTF-8 caracteres
# Esta é uma string literal contendo uma personagem multiplicação multibyte
s = "2 × 2 = 4"
# A seqüência contém 6 bytes que codificam 5 caracteres
s.bytesize # => 6
s.bytesize.times {|i| puts s.getbyte(i), ""} # Mostra "50 195 151 50 61 52"
s.length # => 5
s.length.times {|i| print s[i], ""} # imprime "2 × 2 = 4"
s.setbyte(5, s.getbyte(5) +1); # s é agora "2 × 2 = 5"
```

<p>Note-se que a primeira linha deste código é um comentário de codificação que define a codificação de origem (consulte Especificando Encoding Program) para UTF-8. Sem este comentário, o Ruby intérprete não saberia como decodificar a seqüência de bytes
na seqüência literal em uma seqüência de caracteres.</p>

<p>Quando uma string contém caracteres codificados com números variados
de bytes, já não é possível mapear diretamente do personagem
índice para deslocamento de byte na seqüência. Na seqüência acima, para
exemplo, o segundo carácter começa no segundo byte. Mas o
terceiro personagem começa no quarto byte. Isto significa que você
não pode assumir que o acesso aleatório a caracteres arbitrários dentro de um
string é uma operação rápida. Quando você usa o operador [], como
fizemos no código acima, para acessar um caractere ou substring dentro
uma string multibyte, a implementação de Ruby deve internamente iterar
sequencialmente na cadeia para encontrar o índice do caractere desejado.
Em geral, portanto, você deve tentar fazer o seu processamento de cadeia
utilizando algoritmos seqüenciais, quando possível. Ou seja: usar o iterator each_char quando possível, em vez
de chamadas repetidas para o []
operador. Por outro lado, não é geralmente necessário se preocupar
muito sobre isso. Implementações Ruby otimizar os casos que
pode ser otimizado, e se uma string é composta inteiramente de 1 byte
personagens, o acesso aleatório a esses personagens vão ser eficiente. Se
você quiser tentar suas próprias otimizações, você pode usar a instância
método ascii_only? para determinar
se uma seqüência consiste inteiramente de 7-bit ASCII.</p>

<p>A classe String no Ruby 1.9
define uma codificação
método que retorna a codificação de uma string (o retorno
valor é um objeto de codificação,
o qual é descrito em baixo):</p>

``` ruby 
# - * - Coding: utf-8 - * -
s = "2 × 2 = 4" # caráter multiplicação #  Note multibyte
s.encoding # => <Encoding: UTF-8>
```

<p>A codificação de strings é sempre o mesmo que o
codificação de origem do arquivo, exceto que os literais que contêm
Escapes \u sempre são codificados em
UTF-8, independentemente da fonte de codificação.</p>


<p>Determinadas operações de corda, tais como concatenação e padrão
correspondência, exigem que duas strings (ou uma corda e um regular
expressão) possuem uma codificação compatíveis. Se você concatenar um ASCII
string com uma string UTF-8, por exemplo, obter uma string UTF-8.
Não é possível, no entanto, para concatenar uma string UTF-8 e um
SJIS string: as codificações não são compatíveis, e uma exceção será
ser levantada. Você pode testar se duas strings (ou uma corda e um
expressão regular) possuem uma codificação compatíveis usando a classe
método Encoding.compatible?.
Se as codificações dos dois argumentos são compatíveis,
retorna a uma que é o superconjunto do outro. Se o
codificações são incompatíveis, ela retorna nil.</p>

<p>Você pode definir explicitamente a codificação de uma string com force_encoding. Isso é útil se você tiver
uma seqüência de bytes (leia-se de um fluxo de I / O, talvez) e quer
dizer Ruby como eles devem ser interpretados como caracteres. Ou, se você
ter uma seqüência de caracteres de vários bytes, mas você deseja indexar
bytes individuais com []:</p>

``` ruby
text = stream.readline.force_encoding("utf-8")
bytes = text.dup.force_encoding("binário")
```

<p>
<b>force_encoding</b>
não fazer uma cópia de seu receptor, que modifica o
codificação da string e retorna a string. Este método não
que qualquer caractere conversão dos bytes subjacentes da cadeia são
não mudou, apenas a interpretação de Ruby deles é alterado. O
argumento para force_encoding pode ser
o nome de uma codificação ou um objeto de codificação.
</p>

<p>O force_encoding não faz validação, mas não verifica se os bytes subjacentes do
cadeia de representar uma seqüência válida de caracteres especificada no
de codificação. Use valid_encoding? para
executar a validação. Este método de instância não tem argumentos e verifica se
os bytes de uma cadeia pode ser interpretada como uma seqüência válida de
caracteres usando codificação da seqüência:</p>

``` ruby
s = "\xa4".force_encoding ("utf-8") # Este não é um UTF-8 válido cadeia
s.valid_encoding? # => False
```

<p>O método de codificar de uma string é bastante
diferente da force_encoding. Ele retorna uma string que representa a mesma seqüência de caracteres como
seu receptor, mas usando uma codificação diferente. A fim de alterar o
codificação de ou-transcodificar uma string como esta,
o método de codificar deve alterar o
bytes subjacentes que compõem a cadeia. Aqui está um exemplo:
</p>

``` ruby
# - * - Coding: utf-8 - * -
EURO1 = "\u20AC" # Comece com o personagem Euro Unicode
puts EURO1 # Imprime "€"
euro1.encoding # => <Encoding:UTF-8>
euro1.bytesize # => 3

euro2 = euro1.encode ("iso-8859-15") # transcode para a América-15
puts euro2.inspect # Imprime "\xA4"
euro2.encoding # => <Encoding:iso-8859-15>
euro2.bytesize # => 1

euro3 = euro2.encode ("utf-8") # transcode de volta para UTF-8
EURO1 == euro3 # => true
```

<p>Note que você não deve muitas vezes precisa usar o método de codificação. O tempo mais comum para
transcode cordas é antes de escrevê-las em um arquivo ou enviá-los
através de uma conexão de rede. E, como veremos em Streams e codificações, classes Ruby I/O, apoiar o
transcodificação automática de texto quando ele é gravado.</p>

<p>Se a seqüência que você está chamando de codificar consiste de bytes não codificados, vocês
precisa especificar a codificação, que para interpretar esses bytes
antes de transcodificação-los para outra codificação. Faça isso passando dois
argumentos para codificar. O primeiro
argumento é a codificação desejada, eo segundo argumento é o
codificação atual da cadeia. Por exemplo:</p>

``` ruby
# Interpretar um byte como um codepoint iso-8859-15, e transcodificar para UTF-8
byte = "\xA4"
char = byte.encode("utf-8", "iso-8859-15")
```

<p>Isto é, as duas seguintes linhas de código tem o mesmo efeito:</p>

``` ruby
text = bytes.encode(to, from)
text = bytes.dup.force_encoding(from).encode(to)
```

<p>Se você ligar para codificar sem
argumentos, ele transcodifica seu receptor para o padrão interno
codificação, caso tenha sido definido com o E-ou-U opções intérprete (ver Opções de codificação). Isso permite que os módulos de biblioteca (por
exemplo) para transcodificar suas constantes de cadeias públicas para um comum
codifica para a interoperabilidade.</p>

<p>Codificações de caracteres diferentes não só no seu mapeamento de
bytes para caracteres, mas no conjunto de caracteres que podem
representam. Unicode (também conhecido como UCS-o Universal Character Set) tenta
permitir que todos os personagens, mas codificações de caracteres não baseadas em Unicode
só pode representar um subconjunto de caracteres. Não é possível,
portanto, para transcodificar todos os UTF-8 cordas para EUC-JP (por exemplo);
Caracteres Unicode que não são nem latim, nem japonês não pode ser
traduzida.</p>

<p>Se a codificar ou codificar! método encontra um personagem que não pode transcodificar, ele gera uma exceção:</p>

``` ruby
"\U20AC".Encode("iso-8859-1") # Nenhum sinal de euros em Latin-1, para levantar exceção
```

<p>codificar e codificar! aceitar um hash de transcodificação
opções como seu argumento final. No momento da redação deste texto, o
Nome de opção só é definido: inválido, eo único valor definido para
essa chave é :ignore. "ri
String.encode "dará mais detalhes quando as opções são mais
implementadas.
</p>