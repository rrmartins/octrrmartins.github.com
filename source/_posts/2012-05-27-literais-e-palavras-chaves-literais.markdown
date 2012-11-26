---
layout: post
title: "Literais e Palavras-Chaves Literais"
date: 2012-05-27 12:15
comments: true
categories: 
- Array
- Hash
- String
- Ruby
- Ruby 1.8
- Ruby 1.9
- The Ruby Programming Language
---

<p>Hoje vamos continuar falando de <a href="http://www.ruby-doc.org/core-1.9.2/">Ruby</a>, é hora de nos aprofundar falando um pouco de Literais e Plavras-chaves literais</p>

<h1>Literais e Plavras-chaves literais</h1>

Literais são valores como 1.0, "Hello world", e [] que são incorporados diretamente em seu texto do programa.
<!-- more -->
É interessante notar que muitos literais, tais como números, são as principais expressões - as expressões mais simples possíveis não
compostas de expressões simples. Literais, tais como Array e literais de hash e strings duplas citadas que usam interpolação, incluem
sub-expressões e são, portanto, expressões não primárias.

Determinadas palavras-chave em Ruby são expressões primárias e podem ser considerados palavras-chave literais ou formas especializadas
de referência da variável:

	nil            ->   Avalia o valor nulo, de NilClass classe.
	true           ->   Avalia-se à instância singleton da Classe TrueClass, que um objeto representa o valor booleano 
	                    verdadeiro.
	false          ->   Avalia-se à instância singleton da Classe FalseClass, que um objeto representa os valores booleanos
	                    falsos.
	self           ->   Auto avalia o objeto atual.
	__FILE__       ->   Avalia a uma string que nomeia o arquivo que o Ruby Intérprete (IRB) está em execução. Isto pode ser
		                útil em erro de mensagens.
	__LINE__       ->   Avalia como um inteiro que especifica o número da linha dentro da linha do código atual do __FILE__.
	__ENCODING__   ->   Avalia a um objeto Encoding que especifica a codificação do arquivo atual. (Ruby 1.9 apenas.)

É isso ai galera, até o proximo post.. :)