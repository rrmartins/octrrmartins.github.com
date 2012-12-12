---
layout: post
title: "[TRADUÇÃO] - Trabalhando com time zones em Ruby on Rails"
date: 2012-12-02 09:37
comments: true
categories:
- Ruby
- Tradução
- elabs
- TimeZone
- Rails
---

Conversando com o [CJ Kihlbom][1], um cara super gente boa. :) E ele liberou ai a tradução do post [Working with time zones in Ruby on Rails][2].
- - -
Tradução
- - -
[Rails][3] oferece ótimas ferramentas para trabalhar com fusos horários, mas ainda há um monte de coisas que
podem dar errado. Este blog tem como objetivo lançar alguma luz sobre essas pegadinhas e oferecer soluções
para os problemas mais comuns.
<!--more-->

O que, provavelmente, tem me enganado é que a maioria das vezes o fato de que os tolos do Rails acredita que
você tem tudo coberto o tempo todo **(com o perdão do trocadilho)**. Não me interpretem mal. Eu uso Rails
para fazer o trabalho, tanto para mim quanto possível. Mas eu aprendi da maneira mais difícil que eu não
posso fugir não sabendo quando e como o Rails está me ajudando. Outra pegadinha é o fato de que você tem mais
fusos horários em jogo do que você pode acreditar. Considere o seguinte: db, servidor, a máquina dev, sistema
configurado, o usuário específico configurado e o navegador.

###Configure sua app Rails

Então, quais as ferramentas que temos à nossa disposição como desenvolvedores [Rails][3]? O mais importante é
a configuração `config.time_zone` em seu arquivo `config/application.rb`. ActiveRecord irá ajudá-lo a
converter de e para UTC (o que a documentação não explica) e o fuso horário de sua escolha. Isto significa
que, se tudo que você está fazendo é ter usuários postando varias vezes através de um formulário e utilizar
`Active Record` que ele persista que você é bom para ir.

###Processamento de informações de time

Então, o que sobre o fato de fazer algo com a informação de `time` antes de aceitá-lo? Que é quando se torna
complicado.

####Parsing

Ao analisar informações de `time` é importante nunca fazê-lo sem especificar o fuso horário. A melhor maneira
de o fazer é utilizar `Time.zone.parse` (que vai usar na zona de tempo especificado no `config.time_zone`) em
vez de apenas `Time.parse` (que vai usar a zona do computador).

####Trabalhar com atributos numéricos e ActiveRecord

Chamadas de método como `2.hours.ago` usa o fuso horário que você configurou, portanto, use este se você
pode! A mesma coisa é verdade para atributos de tempo em modelos `ActiveRecord`.

```ruby Time
post = Post.first
post.published_at #=> Thu, 22 Mar 2012 00:00:00 CDT -05:00
```

`ActiveRecord` busca a hora `UTC` do banco de dados e converte para o fuso horário em `config.time_zone` para
você.

####Date vs Time

`Time` tem informações de `Date`, mas `Date` não tem informação de `Time`. Mesmo que você não acha que se
importa, você pode perceber que você faz, mais cedo ou mais tarde. Seja seguro e use `Time` (ou `DateTime`,
se você precisa de suporte para `Times` muito longe do presente).

Mas vamos dizer que você está preso com uma `Date` que você precisa para tratar como um `Time`, pelo menos,
certifique-se de convertê-lo para o seu fuso horário configurado:

```ruby Date vs Time
1.day.from_now # => Fri, 02 Mar 2012 22:04:47 JST +09:00
Date.today.to_time_in_current_zone # => Fri, 02 Mar 2012 00:00:00 JST +09:00
```

####Consultando

Desde que Rails sabe que a sua informação de `Time` é armazenado como UTC no banco de dados que irá converter
a qualquer momento que você dá para o UTC.

```ruby Query
Post.where (["posts.publised_at>?", Time.zone.now])
```

Só não se esqueça de nunca construir a seqüência de consulta à mão e use sempre `Time.zone.now` como a base e
você deve ser seguro.

###Trabalhando com APIs

####Fornecimento

A construção de uma API web para o consumo de outros? Certifique-se sempre de enviar todos os dados de tempo
como `UTC` (e especificar que este é o caso).

``` ruby Time
Time.zone.now.utc.iso8601 # => "2012-03-16T14: 55:33 Z"
```

Leia mais sobre por ISO8601 é aconselhável aqui: [iso8601-dates-in-ruby][4]

####Consumindo

Quando você começa a informação do `Time` a partir de uma API externa que você não tem controle sobre o que
você simplesmente necessita de descobrir o formato e o fuso horário que é enviado a você. Porque
`Time.zone.parse` pode não funcionar com o formato que você recebe, pode precisar de usar:

```ruby Time
Time.strptime(time_string, '%Y-%m-%dT%H:%M:%S%z').in_time_zone(Time.zone)
```

Por que não há nenhum método `#strptime` em `Time.zone` quando há um `#parse`. No entanto, não se esqueça de
chamar `in_time_zone` (`Time.zone`) no seu resultado!

###Trabalhar com vários fusos horários do usuário

Muitos sistemas necessita de suporte aos usuários para entrar e visualizar as informações de tempo em uma
variedade de zonas de tempo. Para conseguir isso, você precisa armazenar zona de cada usuário tempo
(provavelmente só um dos nomes de zona de tempo `String` encontrado no `rake time:zones:all`). Então, para
realmente usar esse fuso horário o padrão mais comum é simplesmente criar um método particular em sua
`ActionController` e executá-la antes como um filtro.

```ruby Time
before_filter :set_time_zone

def set_time_zone
  Time.zone = current_user.time_zone if current_user
end
```

Isso vai fazer a mesma coisa que `config.time_zone` mas em uma base por pedido. Eu ainda recomendo mudar o
`config.time_zone` padrão para um fuso horário que é um bom padrão para seus usuários.

####Testando

Todos acima é algo que os testes devem pegar para você. O problema é que você como o usuário e seu
computador como o servidor de desenvolvimento, acontece a residir no mesmo fuso horário. Esse raramente é o
 caso, uma vez que você levar as coisas para a produção.

Highgroove liberado apenas [Zonebie][5], uma gem que o ajuda a lidar com isso. Eu não tive tempo de testá-lo
eu mesmo ainda, mas parece muito promissor. Se você achar que isso seja um exagero, pelo menos,
certifique-se de que seus testes executados com um conjunto `Time.zone` para outro fuso horário do que a sua
máquina de desenvolvimento está configurado!

####Bug no Time.zone.parse

Jarkko Laine ([@Jarkko][6]) apontou que não há atualmente um bug no `Rails` que pode fazer o
`Time.zone.parse` perder uma hora quando o tempo do sistema está em `DST` (`horário de verão`) e seu fuso
horário configurado não. Jarkko postou um problema no `Rails` rastreando o assunto e escreveu um patch para
corrigir o bug. Até que o patch foi aceito ou se você está rodando com versões mais antigas do `Rails` a
única forma segura de evitar este erro, quer seja um patches para `Rails` em seu aplicativo com correção
Jarkko’s ou uso:

```ruby Time
# use
ActiveSupport::TimeWithZone.new(nil, Time.zone, DateTime.parse("2012-03-25 03:29"))
# => Sun, 25 Mar 2012 03:29:00 PDT -07:00

# or if possible pass the time zone in the string
Time.zone.parse("2012-03-25 03:29 PDT")
# => Sun, 25 Mar 2012 03:29:00 PDT -07:00

# instead of
Time.zone.parse("2012-03-25 03:29")
# => Sun, 25 Mar 2012 04:29:00 PDT -07:00
```

Deve, contudo, ser mencionado que é muito raro que esta superfícies de bug e quando ele faz isso só pode
perder uma hora. Se você pode viver com o que você provavelmente faz melhor por apenas aguardando o patch
para ser aceito.

###Cheat Sheet

####FAZER

```ruby Date vs Time
2.hours.ago # => Fri, 02 Mar 2012 14:02:42 CET +01:00
1.day.from_now # => Fri, 02 Mar 2012 22:04:47 JST +09:00
Date.today.to_time_in_current_zone # => Fri, 02 Mar 2012 00:00:00 JST +09:00
Time.zone.parse("2012-03-02 16:05:37") # => Fri, 02 Mar 2012 16:05:37 JST +09:00
Time.zone.now # => Sat, 03 Mar 2012 00:07:37 JST +09:00
Time.zone.today # If you really can't have a Time or DateTime for some reason
Time.zone.now.utc.iso8601 # "When supliyng an API (you can actually skip .zone here, but I find it better to always use it, than miss it when it's needed)
Time.strptime(time_string, '%Y-%m-%dT%H:%M:%S%z').in_time_zone(Time.zone) # If you can't use parse
```

####NÃO FAZER

```ruby Date vs Time
Time.now # => 2012-03-02 16:05:37 +0100
Date.today.to_time # => 2012-03-02 00:00:00 +0100
Time.parse("2012-03-02 16:05:37") # => 2012-03-02 16:05:37 +0100
Time.now # => 2012-03-02 16:07:20 +0100
Date.today # This could be yesterday or tomorrow depending on the machine's time zone!
Time.strptime(time_string, '%Y-%m-%dT%H:%M:%S%z') # You won't have the time in your configured time zone!
```

###Epílogo

Espero que você tenha aprendido alguma coisa com este post. Tenho certeza que fiz ao escrevê-lo! Se você tem
algum comentário sobre como ele pode ser melhorada, ou se você encontrar algum erro, por favor me avise por
postar um comentário abaixo!


- - -
Agradeço ao amigo [CJ Kihlbom][1], abraços amigo... :D
- - -



[1]: https://twitter.com/cjkihlbom
[2]: http://www.elabs.se/blog/36-working-with-time-zones-in-ruby-on-rails
[3]: http://api.rubyonrails.org
[4]: http://devblog.avdi.org/2009/10/25/iso8601-dates-in-ruby/
[5]: https://github.com/highgroove/zonebie
[6]: https://twitter.com/#!/jarkko
