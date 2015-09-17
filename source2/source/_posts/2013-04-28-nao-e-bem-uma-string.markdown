---
layout: post
title: "Não é bem uma String"
date: 2013-04-28 18:35
comments: true
categories:
- Ruby
- String
- Ruby 2.0
- Eloquent Ruby
---
<!--more-->
Continuando o ultimo post [As Duas Faces de String]({{ root_url }}/blog/2013/04/28/as-duas-faces-de-string/)
que finaliza com a pergunta:

    Então, por que o Ruby nos fornece tanto?

A resposta é que nós tendemos a usar cadeias de caracteres em nosso código para duas finalidades diferentes:
O primeiro, e mais óbvio, o uso de `strings` é manter alguns dados que estamos processando. Leia naqueles
objetos livro a partir do banco de dados e você muito provavelmente vai ter suas mãos cheias de dados de
`string`, coisas como o título do livro, o autor, o nome do AOS, e o texto real.

A segunda maneira que nós usamos cadeias de caracteres é para representar as coisas em nossos programas,
coisas como querer encontrar `:all` registros em uma tabela. A principal coisa sobre: ​​tudo em nosso exemplo
de `Books` é que ActiveRecord pode reconhecê-lo quando vê-lo - o código precisa saber quais registros para
voltar, e `:all` é um flag que diz que ele deve retornar cada um. O bom de usar algo como `:all` para esse
tipo de "significa" que ele também faz sentido para os seres humanos: Você é muito mais propenso a reconhecer
que `:all` quando você se depara com ele do que `0`, ou `-1`, ou mesmo `0x29ef`(Deus me perdoe!).

Estes dois usos para cadeias de caracteres - para tarefas de processamento de dados regulares sobre a
um lado, e, postos de trabalho, do tipo marcador simbólicos internos sobre o outro - faz muita
diferença sobre demandas de objetos. Se você estiver processando dados, você vai querer ter toda a gama de
ferramentas de manipulação de `string` na ponta dos dedos: Você pode querer os dez primeiros caracteres do
título, ou você pode querer obter o seu comprimento ou ver se ele corresponde a alguma expressão regular. Por
outro lado, se você estiver usando alguns caracteres para estar em algo no seu código, você provavelmente não
está muito interessado em brincar com os caracteres reais. Em vez disso, neste segundo caso, você só precisa
saber se essa coisa é o flag que fala para você encontrar todos os registros ou apenas o primeiro registro.
Principalmente, quando você quer alguns caracteres para representar algo, você simplesmente precisa saber se
esta é a forma rápida e confiável.

Até mais galera! :)