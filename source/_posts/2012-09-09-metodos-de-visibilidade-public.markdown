---
layout: post
title: "Metodos de Visibilidade: Public, Protected, Private - #Ruby 1.9"
date: 2012-09-09 19:31
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
<b>Metodos de Visibilidade: Public, Protected, Private</b>...</p>

<h1>Metodos de Visibilidade: Public, Protected, Private</h1>

Métodos de instância podem ser `public`, `private` ou `protected`. Se você já programou com outras linguagens orientadas a objeto, você já pode estar
familiarizado com esses termos. Preste atenção de qualquer maneira, porque estas palavras têm um significado um pouco diferente em Ruby do que em
outras Linguagens.

Métodos são normalmente `public` a menos que explicitamente declarado ser `private` ou `protected`. Uma exceção é o método `initialize`, que é sempre
implicitamente privada. Outra exceção é qualquer método "global" declarado fora de uma definição de classe desses métodos são definidos como métodos 
privados de instância de objeto. Um método `public` pode ser chamado em qualquer lugar, não há restrições sobre seu uso.

Um método `private` é uma implementação interna de uma classe, e que só pode ser chamada por métodos de outra instância da classe (ou, como veremos
mais tarde, as suas subclasses). Métodos `private` são implicitamente invocado em si mesmo, e não podem ser explicitamente chamado em um objeto. Se `m` 
é um método `private`, então você deve chamá-lo em estilo funcional como `m`. Você não pode escrever `o.m` ou mesmo `self.m`.

Um método `protected` é como um método `private` em que só pode ser chamado de dentro da implementação de uma classe ou suas subclasses. Ela difere de
um método `private` na medida em que pode ser explicitamente chamado em qualquer instância da classe, e ele não se restringe a invocação implícita em
si mesmo. Um método `protected` pode ser usado, por exemplo, para definir um acessor que permite que instâncias de uma classe podem compartilhar o
estado interno com o outro, mas não permite que os utilizadores de classe possam acessar esse estado.

Métodos `protected` são os menos comumente definido e também o mais difícil de entender. A regra sobre quando um método `protected` pode ser invocado
pode ser mais formalmente descritos como segue: um método `protected` definido por uma classe C pode ser invocado em um `objeto o` através de um método
em um `objeto p` se e somente se as classes de `o` e `p` são ambos subclasses ou iguais para, a classe C.
<!--more-->
Métodos de visibilidade é declarado com três métodos chamados `public`, `private` e `protected`. Estes são os métodos de instância da Classe do módulo. 
Todas as classes são módulos, e dentro de uma definição de classe, a classe auto-refere-se ao que está sendo definido. Assim, `public`, `private` e
`protected` pode ser usado como se fossem palavras-chave da linguagem. Em fato, no entanto, são chamadas de método em si. Há duas maneiras para chamar
esses métodos. Sem argumentos, eles especificam que todas as definições de método subsequentes terão a visibilidade especificada. Uma classe pode
usá-los como este:

``` ruby Visibilidade de Metodos
class Point
  # Métodos públicos aqui

  # Os seguintes métodos são protegidos
  protected

  # Métodos protegidos aqui

  # Os seguintes métodos são privados
  private

  # Métodos privadas aqui
end
```

Os métodos também podem ser invocados com os nomes de um ou mais métodos (como `symbols` ou `strings`) como argumentos. Quando chamado assim, eles
alteram a visibilidade dos métodos chamados. Neste uso, a declaração de visibilidade deve vir após a definição do método. Uma abordagem consiste em
declarar todos os métodos `private` e `protected` de uma só vez, no final de uma classe. Outra abordagem é declarar a visibilidade de cada método
`private` ou `protected` imediatamente após sua definição. Aqui, por exemplo, é uma classe com um método de utilidade `private` e um método de acesso
`protected`:

``` ruby Visibilidade de Metodos
class Widget
  def x                       # método de acesso para @x
    @x
  end
  protected :x                # Faça-o protegido

  def utility_method          # Define um método
    nil
  end
  private :utility_method     # E torná-lo privado
end
```

Lembre-se que `public`, `private` e `protected` aplicam-se apenas aos métodos em Ruby. Variáveis de instância e de classe são encapsuladas e 
efetivamente `private`, e constantes são efetivamente `public`. Não há nenhuma maneira de fazer uma variável de instância acessível a partir de fora de
uma classe (exceto pela definição de um método de acesso, é claro). E não há maneira de definir uma constante que é inacessível para uso externo.

Ocasionalmente, é útil para especificar que um método de classe deve ser privado. Se sua classe define métodos de fábrica, por exemplo, você pode
querer fazer o novo método `private`. Para fazer isso, use o método `private_class_method`, especificando um ou mais nomes de métodos como símbolos:

``` ruby private_class_method
private_class_method :new
```

Você pode tornar público um método `private` de classe novamente com `public_class_method`. Nenhum método pode ser chamado sem argumentos na forma que
o `public`, `protected` e `private` pode ser.

Ruby é, por definição, uma linguagem muito aberta. A capacidade de especificar que alguns métodos são `private` e `protected` encoraja p bom estilo de
programação, e evita o uso inadvertido de métodos que não fazem parte da API pública de uma classe. É importante entender, no entanto, que os recursos
de metaprogramação de Ruby tornam trivial para invocar métodos `private` e `protected` e até mesmo para acessar variáveis de instância encapsuladas.
Para chamar o método `private` `utility` definida no código anterior, você pode usar o método de envio, ou você pode usar `instance_eval` para avaliar
um bloco no contexto do objeto:

``` ruby instance_eval
w = Widget.new                      # Criar um Widget
w.send :utility_method              # Invoke método privado!
w.instance_eval { utility_method }  # Outra forma de invocá-lo
w.instance_eval { @x }              # Ler variável de instância de w
```

Se você quiser chamar um método pelo nome, mas você não quer inadvertidamente invocar um método `private` que você não sabe aproximadamente, você pode
(no Ruby 1.9) usar `public_send` em vez de `send`. Ele funciona como `send`, mas não invoca métodos `private` quando chamado com um receptor
específico.

É isso ai amigos...

Até o proximo.. =D
