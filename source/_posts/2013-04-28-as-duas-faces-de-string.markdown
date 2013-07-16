---
layout: post
title: "As duas faces de String"
date: 2013-04-28 16:30
comments: true
categories:
- String
- Ruby
- Ruby 2.0
- Symbol
---
<!--more-->
Às vezes, uma boa maneira de explicar um assunto problemático é se engajar em uma ficção um pouco criativa. Você começa com uma
explicação simplista e, uma vez que se aprofunda um pouco, você trabalha o seu caminho de lá para voltar para o mundo real.
Com este espírito, vou começar a exploração de símbolos com uma ligeira simplificação: Símbolos são realmente apenas strings.
Isto não é tão improvável quanto parece: Pense sobre a string "dog" e seu primo mais próximo simbólico :dog. A única coisa que
bate no rosto sobre esses dois objetos é que eles são ambos essencialmente três personagens: um "d", um "o", e "g".

Strings e símbolos também são razoavelmente permutáveis ​​em código na vida real: Tome este exemplo familiar de algum de código no
ActiveRecord, que encontra todos os registros no livros de mesa:

```
book = Book.find(:all)
```

O argumento para o método `find` é simplesmente um flag, há que dizer que queremos encontrar todos os registros de livros, e não
apenas o primeiro registro, e não apenas o último registro, mas todos eles. O valor real que passamos para `Book.find` realmente
não importa muito. Podemos imaginar que, se tivesse tempo e motivação, podemos ir nas entranhas do `ActiveRecord` e reescrever o
código para que pudéssemos usar uma string para sinal de que queria que todos os livros:

```
book = Book.find('all')
```

Portanto, não é a minha explicação simplificada de símbolos: Além do fato de que a digitação de `:all` exige um keystroke menos
do que digitar `'all'`, não há realmente muito a distinguir um símbolo a partir de uma string. Então, por que o Ruby nos fornece tanto?

Continuando no post [Não é bem uma String]({{ root_url }}/blog/2013/04/28/nao-e-bem-uma-string/)...

Até mais galera! :)