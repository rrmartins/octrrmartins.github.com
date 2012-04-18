---
layout: post
title: "O Mundo Hash no Ruby 1.9.2 - Parte III - Hashs Codes"
date: 2012-05-10 13:01
comments: true
categories:  
- Hash
- Hash Codes
- Codes
- Equals
- Ruby
- Ruby 1.8
- Ruby 1.9
- The Ruby Programming Language
---

<p>Continuando os estudos de Ruby, e a leitura do livro The Ruby Programming Language</p>
<p>Hoje vamos falar um pouco mais de Hash, é hora de nos aprofundar.</p>

<h3>Códigos de Hash, Igualdade e mutáveis ​​Chaves</h3>

Hashs[1] de Ruby são implementadas, sem surpresa, com um conjunto de dados de
estrutura conhecida como uma tabela hash. Objetos usados como chaves em um hash deve ter um método chamado de hash que retorna <a href="http://www.ruby-doc.org/core-1.9.2/Fixnum.html">Fixnum</a> hashcode para a chave. Se duas chaves são iguais, elas devem ter o mesmo
hashcode. Chaves desiguais também pode ter a mesma hashcode, mas hash's de tabelas são mais eficientes quando hashcodes duplicados são raros.

A classe Hash compara chaves igualdade com o método <a href="http://www.ruby-doc.org/core-1.9.2/Hash.html#method-i-eql-3F">eql?</a>. Para
outras classes Ruby, eql? obras como o operador == . Se você definir uma nova classe que
substitui o método eql?, você deve também substituir o método <a href="http://www.ruby-doc.org/core-1.9.2/Hash.html#method-i-hash">hash</a>, ou se não as instâncias de sua classe não vam funcionar como chaves em um hash.

Se você definir uma classe e não sobreescrever o método eql?, em seguida, as instâncias dessa classe são
comparadas com a identidade do objeto quando usado como chave de hash. Duas instâncias distintas de sua classe são distintas chaves de hash mesmo que eles representam o mesmo conteúdo. Neste caso, o método padrão hash é apropriado: retorna a única object_id do objeto.

Note-se que objetos mutáveis ​​são problemáticos como chaves de hash. Mudar o conteúdo de um objeto geralmente muda seu hashcode. Se você usar um objeto como uma chave e então alterar esse objeto, a tabela de hash ficará corrompida, e o hash não funciona corretamente.

Porque strings são mutáveis, mas comumente usamos chaves de hash, Ruby trata eles como um caso especial e faz cópias privadas de todas as cadeias utilizadas como chaves. Este é o único caso especial, no entanto, você deve ser muito cuidado ao usar qualquer outro objeto mutável como uma chave hash. Considera fazendo uma cópia privada ou chamando o método <a href="http://ruby-doc.org/core-1.9.2/Object.html#method-i-freeze">freeze</a>.
Se você deve usar chaves de hash mutável, chame o método <a href="http://www.ruby-doc.org/core-1.9.2/Hash.html#method-i-rehash">rehash</a> do Hash cada vez que você alterar uma chave.

-> <a href="http://www.ruby-doc.org/core-1.9.2/Hash.html">Hash</a>

Até a proxima!