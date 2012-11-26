---
layout: post
title: "Desenvolvendo Um Chat com NodeJS"
date: 2012-04-30 17:34
comments: true
categories: 
- NodeJS
- RealTime
- BackEnd
- FrontEnd
- JavaScript
---
Olá Developers...

Vamos desenvolver um pequeno Chat em NodeJS... Estou começando com os estudos de NodeJS tambem, e tem sido de grande valia para mim...

Então, antes de mais nada, vamos fazer o download do NodeJS no link <a href="http://nodejs.org/#download">nodejs.org</a>, ou podemos instalar com o <a href="https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager">Node Package Manager (npm)</a>. Eu estou usando o npm, e indico a todos... :D
<!-- more -->
Vamos criar a aplicação, crie uma pasta com o nome da aplicação:

{% codeblock Criando Pasta %}
mkdir app_chat
{% endcodeblock %}

Agora vamos entrar na pasta do projeto, e criar o arquivo que será nosso servidor, nome exemplo: heloword_server.js.

Hora de começar a brincar com o servidor.

``` javascript Criando o server
var fs = require('fs');
var server = require('http').createServer(function(req, response){
  fs.readFile('helloworld.html', function(err, data){
    response.writeHead(200, {'Content-Type':'text/html'});  
    response.write(data);  
    response.end();
  });
});
```

Na linha 1, temos um require('fs'), este é um framework do nodejs, que serve para trabalhar com arquivos.

Na linha 2, estamos instanciando e criando o server 'http'.

Na linha 3, já dentro da função do createServer, temos a leitura do arquivo que será alimentado, e uma criação de função.

Na linha 4, estamos escrevendo o arquivo, lembrando que ele será do tipo HTML.

Na linha 5, temos a escrita no arquivo. E abaixo finalizamos.

Este processo de leitura do arquivo, ocorre sempre que alguem entra na aplicação.

Vamos continuar, agora vamos colocar uma porta para o servidor. 

``` javascript Criando uma porta e aplicando ao server
var port = process.env.PORT || 3000;

server.listen(port, function() {
  console.log("Listening on " + port);
});
```

Neste exemplo acima, pegamos o número da porta do processo, ou fica valendo a porta 3000.

Logo depois coloco a port no server, neste exemplo eu criei uma função, só para logar o numero da porta, logo nos logs do servidor estará o numero da porta, quando iniciar.

Vamos agora começar a escrever as funcionalidades do servidor. Vamos escrever as funções que tratam que esta entrando e quem esta saindo da app.

``` javascript Function Connected e Disconnected
var everyone = require("now").initialize(server);

everyone.connected(function(){
  console.log("Joined: " + this.now.name);
});

everyone.disconnected(function(){
  console.log("Left: " + this.now.name);
});
```

Nesta função, instancio o framework '<a href="http://www.nowjs.com/">now</a>', que voce pode esta instalando com o comando: npm install now

Dentro do '<a href="http://www.nowjs.com/">now</a>', temos 2 funções, a <a href="http://www.nowjs.com/doc/symbols/nowjs#event:connect">connected</a> e a <a href="http://www.nowjs.com/doc/symbols/nowjs#event:disconnect">disconnected</a>, nessas funções, estou registrando quem esta se logando e quem esta saindo da aplicação.

Agora vem a parte legal, vamos criar em modo de execução 2 funções, só que uma dentro da outra, ou seja, vamos usar fortemente o conceito de <a href="http://lucastex.com.br/2009/10/21/nao-tenha-medo-das-closures/">closure</a>, mas por favor, não tenha medo disso, parece estranho no inicio, mas acredite o trem é legal...

``` javascript Criando Funções dentro de Funções
everyone.now.distributeMessage = function(message){
	console.log("mensagem "+message);
	message = message.replace(/(<([^>]+)>)/ig,"");
	everyone.now.receiveMessage(this.now.name, message);
};
```

Neste bloco de codigos, estamos criando a função distributeMessage, que esta, receberá a mensagem.

Na linha 2, estou somente logando a mensagem que chegou.

Na liinha 3, estamos tratando com uma expressão regular, a mensagem que chega, para que ninguem possa trollar a app com codigo de javascript, e outras coisas...

Na linha 4, estamos fazendo a chamada da função que esta na view, e ela que imprimi a mensagem no DOM.

Logo o arquivo do helloword_server.js precisa ficar assim:

{% include_code Arquivo do Server NodeJS helloworld_server.js %}


<h3>Criando a View</h3>

A nossa view, será basicamente HTML, e um pouco de simples javascript, então, dessa vez será 0 de explicação.

{% include_code View do Chat helloworld.html %}

Proximo, passo, só levantar o servidor:
{% codeblock Levantando o Servidor %}
node helloworld_server.js
{% endcodeblock %}


É isso ai amigos, segue o lick de como a aplicação ficou no heroku, e o lick do repositorio dela no github.

Heroku -> <a href="http://nodechat.herokuapp.com">nodechat.herokuapp.com</a>

Github -> <a href="https://github.com/rrmartins/node_chat">Node Chat</a>

Até a proxima.. 