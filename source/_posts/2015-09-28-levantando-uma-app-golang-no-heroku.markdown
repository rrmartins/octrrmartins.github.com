---
layout: post
title: "Resolvendo problema com $PORT com uma app Golang no Heroku"
date: 2015-09-28 22:24:09 -0300
comments: true
categories:
- Golang
- Go
- App
- Heroku Host
- Host
- Heroku
- PORT
- os
- Getenv
---

Æ,

Já faz um tempo que venho estudando e praticando um pouco com [Golang](https://golang.org). Como alguns codigos estão somente em meu repo particular, resolvi colocar alguns de meus codígos em algum host para testes iniciais. Optei por usar o [Heroku](heroku.com) (não estou ganhando nada para fazer propaganda kkkkk).

Porém ao seguir o seu [Getting Started with Go](https://devcenter.heroku.com/articles/getting-started-with-go#introduction), percebi uma coisa: não deixam claro as politicas de porta de acesso à app.

Como resolver?
<!--more-->

    Não vou entrar nos detalhes para colocar a app no ar, pois o 'passo-a-passo'
    do Heroku da uma boa ajuda quanto a isso.

Um dos [packages](https://golang.org/pkg/) nativos de Go, que é bem útil para usar no
Heroku, é o package [os](https://golang.org/pkg/os/).

A [func](https://golang.org/doc/effective_go.html#functions) que vamos usar aqui será a [Getenv](https://golang.org/pkg/os/#Getenv) do pacote `os`, ela lê uma variavel de ambiente que é passada como parametro do tipo `string`.

``` go e sua chamada será algo como:
  os.Getenv("")
```

``` go A assinatura da func
  func Getenv(key string) string
```

Ok! Bora para o problema.

Sem a declaração desta chamada, para que a app possa saber qual será a porta que ela vai iniciar. Iremos observar nos logs do Heroku o seguinte erro:

```
  2015-09-28T21:57:58.411180+00:00 heroku[web.1]: Error R10 (Boot timeout) -> \
  Web process failed to bind to $PORT within 60 seconds of launch
```

Já se torna visivel que esta reclamando de algo com relação a variavel de ambiente `$PORT`, e por isso o processo `web` não foi iniciado, recebendo na cara um `SIGKILL`.

Ao ler a app de apresentação que foi publicada para ajuda pelo Heroku, [app Go Getting Started on Github](https://github.com/heroku/go-getting-started/) e ler mais afinco sobre este erro e sobre 'o como o Heroku Apps precisa desta declaração :)', saiu algo assim:

``` go Declaração com a variavel de ambiente $PORT
  os.Getenv("PORT")
```

Lembrem, como estamos usando o pacote `os`, temos que fazer seu `import`.

``` go Importando pacote os
   import "os"
```

Caso, não faça este `import`, verá um erro ao compilar o codigo, algo como:

``` go Erro de compilação
$ go build
# heroku.com/rrmartins/rrtempo
./servidor.go:15: undefined: os
./servidor.go:16: undefined: os
```

Então, faça o `import "os"`. :)

Sabendo a declaração, podemos iniciar o server:

``` go Levantando o server
   port := os.Getenv("PORT")
   http.ListenAndServe(fmt.Sprintf(":%s", port),nil)
```

Na segunda linha do codígo acima, vemos mais dois pacotes nativos Go, `http` e `fmt`, para fazer funcionar é importante tambem fazer o `import` deles, ficando algo parecido com isso:

``` go Importando mais pacotes
import (
  "fmt"
  "os"
  "net/http"
)
```

Mas, como o problema a ser resolvido não é 'o como levantar o servidor', mas sim 'o que é preciso para tal', então vamos voltar ao caso aqui. :)

Declarando só com `port := os.Getenv("PORT")`, é obrigatório que nas configurações da app no Heroku tenha a variavel `$PORT` declarada, e se não tiver? Quem poderá nos defender?

Para não fazer um `if` deste modo:

``` go if desnecessario
  if os.Getenv("PORT") != "" {
    port = os.Getenv("PORT")
  } else {
    port = "8080"
  }
```

Fazemos uma condicional de uma linha só: :)

``` go Condicional lindão
  port := map[bool]string{true: os.Getenv("PORT"), \
  false: "8080"}[ os.Getenv("PORT") != ""]
  http.ListenAndServe(fmt.Sprintf(":%s", port),nil)
```

Vejam só, se não tem a declaração da variavel `$PORT`, assumo a porta `8080`.

É isso ai.

O codígo deste post esta em [Github rrtime](https://github.com/rrmartins/rrtime), e rodando no Heroku [Heroku rrtime](https://rrtime.herokuapp.com/tempo).

Creditos pela ajuda da condicional mais simples ao [Vinicius Souza Twitter](https://twitter.com/iamvsouza) | [Vinicius Souza Github](https://github.com/vsouza) contribuidor da comunidade brasileira [Golang BR](http://www.golangbr.org/).

Valeu!