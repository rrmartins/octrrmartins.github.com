---
layout: post
title: "Criando Combobox com Rails 3"
date: 2012-02-15 13:05
comments: true
categories: 
- Rails
- Rails 3
- Ruby
---
<p>
Para todos aqueles que conhecem o <a href="http://api.rubyonrails.org/classes/ActionView/Helpers/FormHelper.html">Form_for</a>,
 e quem ainda não teve a oportunidade de vê-lo. Vamos estudar um pouco de <a href="http://api.rubyonrails.org/">Rails</a>, e 
 estudem logo, o mercado esta precisando de programadores bons.. :D
 </p>
<!-- more -->
<p>
Então, vamos ao contexto do post, para gerar um combo box semelhante a este abaixo: </p>
<select name="estado">
<option value="AL">AL</option>
<option value="AP">AP</option>
<option value="AM">AM</option>
<option value="BA">BA</option>
<option value="CE">CE</option>
<option value="ES">ES</option>
</select>
<p>
Faça assim:
</p>
``` ruby Criando Combobox em Rails
<%= f.select :estado, ([["AC", "AC"], ["AL", "AL"], ["AP", "AP"],
["AM", "AM"], ["BA", "BA"], ["CE", "CE"], ["DF", "DF"], ["ES", "ES"],
["GO", "GO"], ["MA", "MA"], ["MT", "MT"], ["MS", "MS"], ["MG", "MG"],
["PA", "PA"], ["PB", "PB"], ["PR", "PR"], ["PE", "PE"], ["PI", "PI"],
["RJ", "RJ"], ["RN", "RN"], ["RS", "RS"], ["RO", "RO"], ["RR", "RR"],
["SC", "SC"], ["SP", "SP"], ["SE", "SE"], ["TO", "TO"]]) %>
```
<p>
Até a próxima rubistas... :D