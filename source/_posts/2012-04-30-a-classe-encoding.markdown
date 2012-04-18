---
layout: post
title: "A classe Encoding"
date: 2012-04-30 10:04
comments: true
categories: 
- Ruby
- Ruby 1.8
- Ruby 1.9
- The Ruby Programming Language
---
<p>Continuando os estudos de Ruby, e a leitura do livro The Ruby Programming Language</p>

<p>A classe Encoding de Ruby 1.9 representa uma codificação de caracteres. Objetos de codificação para agir como identificadores opacos para uma codificação e não têm muitos métodos próprios. O nome do método retorna o nome de uma codificação. to_s é um sinônimo para o name, e inspect converte um objeto em uma seqüência de codificação de uma forma mais detalhada do que o name faz.</p>

<p>Ruby define uma constante para cada uma das codificações built-in's que ele suporta, e estes são a maneira mais fácil de especificar uma codificação em seu programa. As constantes predefinidas incluem pelo menos o seguinte:</p>

``` ruby Encoding
Encoding::ASCII_8BIT     # Also ::BINARY
Encoding::UTF_8          # UTF-8-encoded Unicode characters
Encoding::EUC_JP         # EUC-encoded Japanese
Encoding::SHIFT_JIS      # Japanese: also ::SJIS, ::WINDOWS_31J, ::CP932
```
<!--more-->
<p>Observe que, como estas são constantes, eles devem ser escritos em letras maiúsculas e hífens nos nomes de codificação devem ser convertidos para sublinhados. Ruby 1.9 também suporta a codificação US-ASCII, as codificações européias ISO-8859-1 através da ISO-8859-15, e o Unicode UTF-16 e UTF-32 codificações em variantes big-endian e little-endian.</p>

<p>Se você tiver um nome de codificação como uma string e deseja obter o objeto de codificação correspondente, use o método de fábrica Encoding.find:</p>

``` ruby Encoding
encoding = Encoding.find("utf-8")
```
<p>
Usando Encoding.find faz com que a codificação passa a ser carregada dinamicamente, se necessário. Encoding.find aceita names de codificação que são qualquer maiúsculo ou minúsculo. Chame o método name de uma codificação para obter o nome da codificação como uma string.</p>

<p>Encoding.list retorna uma matriz de todos os objetos de codificação disponíveis. Encoding.name_list retorna um array de nomes (como strings) de todas as codificações disponíveis. Codificações muito têm mais de um nome de uso comum, e Encoding.aliases retorna um hash que mapeia aliases de codificação para os names de codificação oficial pelos quais são sinônimos. A matriz retornada por Encoding.name_list inclui os aliases nos Encoding.aliases hash.</p>

``` ruby Encoding.name_list
Encoding.name_list
 => ["ASCII-8BIT", "UTF-8", "US-ASCII", "Big5", "Big5-HKSCS", "Big5-UAO", "CP949", "Emacs-Mule", "EUC-JP", 
 "EUC-KR", "EUC-TW", "GB18030", "GBK", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5",
 "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-10", "ISO-8859-11", "ISO-8859-13", 
 "ISO-8859-14", "ISO-8859-15", "ISO-8859-16", "KOI8-R", "KOI8-U", "Shift_JIS", "UTF-16BE","UTF-16LE", 
 "UTF-32BE", "UTF-32LE", "Windows-1251", "BINARY", "IBM437", "CP437", "IBM737", "CP737", "IBM775", 
 "CP775", "CP850","IBM850", "IBM852", "CP852", "IBM855", "CP855", "IBM857", "CP857", "IBM860", "CP860", 
 "IBM861", "CP861", "IBM862", "CP862", "IBM863","CP863", "IBM864", "CP864", "IBM865", "CP865", "IBM866", 
 "CP866", "IBM869", "CP869", "Windows-1258", "CP1258", "GB1988","macCentEuro", "macCroatian", "macCyrillic", 
 "macGreek", "macIceland", "macRoman", "macRomania", "macThai", "macTurkish","macUkraine", "CP950", "CP951", 
 "stateless-ISO-2022-JP", "eucJP", "eucJP-ms", "euc-jp-ms", "CP51932", "eucKR", "eucTW", "GB2312","EUC-CN", 
 "eucCN", "GB12345", "CP936", "ISO-2022-JP", "ISO2022-JP", "ISO-2022-JP-2", "ISO2022-JP2", "CP50220", "CP50221", 
"ISO8859-1", "Windows-1252", "CP1252", "ISO8859-2", "Windows-1250", "CP1250", "ISO8859-3", "ISO8859-4", 
"ISO8859-5", "ISO8859-6","Windows-1256", "CP1256", "ISO8859-7", "Windows-1253", "CP1253", "ISO8859-8", 
"Windows-1255", "CP1255", "ISO8859-9", "Windows-1254", "CP1254", "ISO8859-10", "ISO8859-11", "TIS-620", 
"Windows-874", "CP874", "ISO8859-13", "Windows-1257", "CP1257","ISO8859-14", "ISO8859-15", "ISO8859-16", 
"CP878", "SJIS", "Windows-31J", "CP932", "csWindows31J", "MacJapanese", "MacJapan","ASCII", "ANSI_X3.4-1968", 
"646", "UTF-7", "CP65000", "CP65001", "UTF8-MAC", "UTF-8-MAC", "UTF-8-HFS", "UCS-2BE", "UCS-4BE","UCS-4LE", 
"CP1251", "UTF8-DoCoMo", "SJIS-DoCoMo", "UTF8-KDDI", "SJIS-KDDI", "ISO-2022-JP-KDDI", 
"stateless-ISO-2022-JP-KDDI","UTF8-SoftBank", "SJIS-SoftBank", "locale", "external", "filesystem", "internal"] 
```

``` ruby Encoding.aliases
	Encoding.aliases
 => {"BINARY"=>"ASCII-8BIT", "CP437"=>"IBM437", "CP737"=>"IBM737", "CP775"=>"IBM775", "IBM850"=>"CP850",
  "CP857"=>"IBM857", "CP860"=>"IBM860", "CP861"=>"IBM861", "CP862"=>"IBM862", "CP863"=>"IBM863", 
  "CP864"=>"IBM864", "CP865"=>"IBM865", "CP866"=>"IBM866", "CP869"=>"IBM869", "CP1258"=>"Windows-1258", 
  "CP950"=>"Big5", "CP951"=>"Big5-HKSCS", "eucJP"=>"EUC-JP", "euc-jp-ms"=>"eucJP-ms", "eucKR"=>"EUC-KR",
  "eucTW"=>"EUC-TW", "EUC-CN"=>"GB2312", "eucCN"=>"GB2312", "CP936"=>"GBK", "ISO2022-JP"=>"ISO-2022-JP",
  "ISO2022-JP2"=>"ISO-2022-JP-2", "ISO8859-1"=>"ISO-8859-1", "CP1252"=>"Windows-1252", "ISO8859-2"=>"ISO-8859-2",
  "CP1250"=>"Windows-1250", "ISO8859-3"=>"ISO-8859-3", "ISO8859-4"=>"ISO-8859-4", "ISO8859-5"=>"ISO-8859-5",
  "ISO8859-6"=>"ISO-8859-6", "CP1256"=>"Windows-1256", "ISO8859-7"=>"ISO-8859-7", "CP1253"=>"Windows-1253",
  "ISO8859-8"=>"ISO-8859-8", "CP1255"=>"Windows-1255", "ISO8859-9"=>"ISO-8859-9", "CP1254"=>"Windows-1254", 
  "ISO8859-10"=>"ISO-8859-10", "ISO8859-11"=>"ISO-8859-11", "CP874"=>"Windows-874", "ISO8859-13"=>"ISO-8859-13",
  "CP1257"=>"Windows-1257", "ISO8859-14"=>"ISO-8859-14", "ISO8859-15"=>"ISO-8859-15", "ISO8859-16"=>"ISO-8859-16",
  "CP878"=>"KOI8-R", "SJIS"=>"Shift_JIS", "CP932"=>"Windows-31J", "csWindows31J"=>"Windows-31J", 
  "MacJapan"=>"MacJapanese", "ASCII"=>"US-ASCII", "ANSI_X3.4-1968"=>"US-ASCII", "646"=>"US-ASCII", 
  "CP65000"=>"UTF-7", "CP65001"=>"UTF-8", "UTF-8-MAC"=>"UTF8-MAC", "UTF-8-HFS"=>"UTF8-MAC", 
  "UCS-2BE"=>"UTF-16BE", "UCS-4BE"=>"UTF-32BE", "UCS-4LE"=>"UTF-32LE", "CP1251"=>"Windows-1251", 
  "locale"=>"UTF-8", "external"=>"UTF-8", "filesystem"=>"UTF-8"} 
```

<p>Use Encoding.default_external e Encoding.default_internal para obter os objetos de codificação que representam os padrões externos e internos de codificação padrão. Para obter a codificação para o local atual, chame Encoding.locale_charmap e passar a seqüência resultante para Encoding.find.</p>

``` ruby Encoding.default_external
Encoding.default_external 
 => #<Encoding:UTF-8> 
```

``` ruby Encoding.default_internal
Encoding.default_internal
 => nil 
```

``` ruby Encoding.locale_charmap
Encoding.locale_charmap
 => "UTF-8"
```

<p>A maioria dos métodos que esperam um objeto Encoding também aceitará um nome de codificação maiúsculas e minúsculas (como ascii, binário, UTF-8, EUC-JP, ou sjis) no lugar de um objeto de codificação.</p>

É isso ai... até a proxima... :D


