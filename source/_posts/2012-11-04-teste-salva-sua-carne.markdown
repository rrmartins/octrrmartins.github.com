---
layout: post
title: "Teste salva sua carne"
date: 2012-11-04 09:30
comments: true
categories: 
- TDD
- BDD
- Testes
- Rails 3 in Action
---

Galera estou lendo mais um livro, e vou passar para vocês um textículo do inicio do [Rails 3 in Action](http://www.amazon.com/Rails-3-Action-Ryan-Bigg/dp/1935182277).

Lendo o capitulo 1, ele deixou uma pergunta no ar: como você faz aplicações Rails sustentável?

A resposta é que você escreve testes automatizados para a aplicação como você desenvolve, e você escreve isso **o tempo todo**.
<!-- more -->
Ao escrever testes automatizados para a sua aplicação, você pode rapidamente assegurar que o aplicativo está funcionando 
como deveria. Se você não escrever testes, a sua alternativa seria a de verificar toda a aplicação manualmente, o que é 
demorado e propício a erros. **Teste automatizado economiza uma tonelada de tempo, a longo prazo e leva a menos bugs.** Os 
seres humanos cometem erros; programas (se codificado corretamente) não.

## Teste e desenvolvimento orientado a comportamento

No mundo `Ruby` uma enorme ênfase é colocada em teste, especificamente  desenvolvimento orientado a testes (Test-driven
 development - TDD) e desenvolvimento de comportamentos (Behavior-Driven Development - BDD). 

Ao aprender técnicas de teste bem como agora, você tem um caminho sólido para se certificar que nada esta quebrado quando 
você começa a escrever sua primeira aplicação real Rails. Se você não testar, não há como dizer o que poderia dar errado 
seu código. 

TDD é uma metodologia que consiste em escrever um caso de teste falho primeiro (geralmente usando uma testar ferramenta 
como `Test::Unit`), em seguida, escrever o código para fazer o teste passar e, finalmente, refatorar o código. Este 
processo é comumente chamado de `vermelho-verde-refatorar`( `red-green-refactor` ). As razões para desenvolver código desta 
forma são duas. Primeiro, faz você considera como o código deve ser executado antes de ser usado por qualquer pessoa. Em 
segundo lugar, crie um teste automatizado, você pode executar quantas vezes você quiser para garantir que o seu código ainda
está trabalhando como pretendido.

BDD é uma metodologia baseada em TDD. Você escreve um teste automatizado para verificar a interação entre as diferentes 
partes da base de código em vez de testar que cada parte funciona de forma independente. 

As duas ferramentas são usadas para o BDD são (RSpec)[http://rspec.info/] e (Cucumber)[http://cukes.info/].

## Test-Driven Development

Uma resposta enigmática mas verdadeira para a pergunta "Por que eu deveria testar?" é "porque você é humano.". Os seres 
humanos - a grande maioria deste livro - comete erros. É uma de nossas favoritas maneiras de aprender. Porque os 
seres-humanos cometem erros, ter uma ferramenta para informar quando eles fazem um erro é útil, não é? Teste automatizado 
fornece uma rápida segurança para informar aos desenvolvedores quando eles cometem erros. Por eles, é claro, queremos dizer
que você. Nós queremos que você faça poucos erros quanto possível. Queremos que salvar sua vida!

TDD e BDD também dar-lhe tempo para pensar nas suas decisões antes de escrever qualquer código. Escrevendo primeiro teste
para a implementação, você é (ou, pelo menos, você deve ser) levado a pensar através da implementação: o código que você 
vai escrever depois do teste e como você vai fazer para o teste passar. Se você encontrar um teste difícil de escrever, 
então talvez a implementação pode ser melhorada. Infelizmente, não há nenhuma maneira clara de quantificar a dificuldade de
escrever um teste e trabalhar com ele para além de consultar outras pessoas que estão familiarizadas com o processo.

Uma vez que o teste é implementado, você deve ir escrever algum código que o seu teste pode passar. Se você está
trabalhando escrevendo o código primeiro e depois os testes para pegar um bug da implementação, é geralmente melhor 
repensar o teste e desfazer sua implementação.

**Primeiro teste, código mais tarde.**

Até mais amigos!
