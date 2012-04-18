---
layout: post
title: "Codificações de String e Caracteres multibyte"
date: 2012-04-24 00:04
comments: true
categories: 
- Ruby
- Ruby 1.8
- Ruby 1.9
- The Ruby Programming Language
---
<p>Olá... Resolvi começar a escrever alguma coisa também de <a href="http://www.ruby-lang.org/pt/">Ruby</a>... Vamos começar com um pouco do inicio da classe <a href="http://www.ruby-doc.org/core-1.9.3/String.html">String</a>.</p>

<h2>Codificações de <a href="http://www.ruby-doc.org/core-1.9.3/String.html">String</a> e Caracteres multibyte</h2>

<p>
<a href="http://www.ruby-doc.org/core-1.9.3/String.html">String</a> são fundamentalmente diferentes em <a href="http://www.ruby-doc.org/core-1.8.7/">Ruby 1.8</a> e <a href="http://www.ruby-doc.org/core-1.9.3/">Ruby 1.9</a>:
</p>
<p>
No <a href="http://www.ruby-doc.org/core-1.8.7/">Ruby 1.8</a>, <a href="http://www.ruby-doc.org/core-1.9.3/String.html">String</a> são uma seqüência de bytes. quando cadeias
são usados ​​para representar texto (em vez de dados binários), cada byte da 
cadeia é considerada para representar um único caractere ASCII. no <a href="http://www.ruby-doc.org/core-1.8.7/">Ruby 1.8</a>, os elementos individuais de uma <a href="http://www.ruby-doc.org/core-1.9.3/String.html">String</a> não são caracteres, mas
números, o valor de byte real ou codificação de caracteres.
</p>
<p>
Em <a href="http://www.ruby-doc.org/core-1.9.3/">Ruby 1.9</a>, por outro lado, as cadeias são sequências verdadeiras
de caracteres, e esses caracteres não necessitam de ser confinado à
Conjunto de caracteres ASCII. Em <a href="http://www.ruby-doc.org/core-1.9.3/">Ruby 1.9</a>, os elementos individuais de uma cadeia
são caracteres representados como cadeias de tamanho igual a 1 - em vez de
inteiros códigos de caracteres. Cada <a href="http://www.ruby-doc.org/core-1.9.3/String.html">String</a> tem uma codificação que
especifica a correspondência entre os bytes na cadeia e
os caracteres representam os bytes. Codificações, como o UTF-8
codificação de caracteres Unicode utilizam número variável de bytes para
cada carácter, e não há mais uma 1-para-1 (nem mesmo uma correspondência 2-para-1)
entre bytes e subseções de caracteres. As subseções que seguem, explicam a codificação relacionada de
características de strings em <a href="http://www.ruby-doc.org/core-1.9.3/">Ruby 1.9</a>, e também demonstram de forma rudimentar o 
suporte para caracteres multibyte no <a href="http://www.ruby-doc.org/core-1.8.7/">Ruby 1.8</a> usando a biblioteca jcode.
</p>

Até o proximo.. :D