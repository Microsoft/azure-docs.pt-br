---
title: Exemplos de consulta Lucene para Azure Search | Microsoft Docs
description: "Sintaxe de consulta Lucene para pesquisa difusa, pesquisa por proximidade, aumento de termos, pesquisa com expressão regular e pesquisa com curinga."
services: search
documentationcenter: 
author: LiamCa
manager: pablocas
editor: 
tags: Lucene query analyzer syntax
ms.assetid: 147f360d-a5ce-4d7b-a909-c8b65bfb748c
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/21/2017
ms.author: liamca
ms.openlocfilehash: 1faed621039ecd04064cb074e6b9011418e6ec47
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="lucene-query-syntax-examples-for-building-queries-in-azure-search"></a>Exemplos de sintaxe de consulta Lucene para criar consultas no Azure Search
Ao construir consultas do Azure Search, você pode usar a [sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) padrão ou a alternativa [Lucene Query Parser no Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). O Analisador de Consulta Lucene dá suporte a constructos de consulta mais complexos, como consultas com escopo de campo, pesquisa difusa, pesquisa por proximidade, aumento de termo e pesquisa de expressão regular.

Neste artigo, execute exemplos em etapas, que demonstram as operações de consulta disponíveis ao usar a sintaxe completa.

## <a name="viewing-the-examples-in-jsfiddle"></a>Exibindo os exemplos no JSFiddle

Todos os exemplos deste artigo são consultas executáveis que são executadas em um índice de Pesquisa pré-carregado no [JSFiddle](https://jsfiddle.net), um editor de códigos online para teste de script e HTML. 

Para executá-los, clique com o botão direito do mouse nas URLs de exemplo de consulta para abrir o JSFiddle em uma janela separada do navegador.

> [!NOTE]
> Os exemplos a seguir aproveitam um índice de pesquisa que consiste em trabalhos disponíveis com base em um conjunto de dados fornecido pela iniciativa [OpenData da cidade de Nova York](https://nycopendata.socrata.com/) . Esses dados não devem ser considerados atuais ou completos. O índice está em um serviço de área restrita fornecido pela Microsoft. Você não precisa de uma assinatura do Azure ou do Azure Search para experimentar essas consultas.
>


## <a name="how-to-invoke-full-lucene-parsing"></a>Como invocar a análise completa do Lucene

Todos os exemplos deste artigo especificam o parâmetro de pesquisa **queryType=full**, indicando que a sintaxe completa é tratada pelo Analisador de Consulta Lucene. 

**Exemplo 1** : clique com o botão direito do mouse no seguinte trecho de código da consulta para abri-la em uma nova página do navegador que carrega o JSFiddle e executa a consulta:

* [&queryType=full&search=*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

Na nova janela do navegador, a origem JavaScript e a saída HTML são apresentados lado a lado. O script referencia uma consulta completa (não apenas o trecho, conforme mostrado no link). A consulta completa é mostrada nas URLs de cada exemplo. 

Essa consulta retorna documentos de nosso índice New York City Jobs (nycjobs, carregado em um serviço de área restrita). Para resumir, a consulta especifica o retorno apenas de cargos executivos. A consulta subjacente completa é a seguinte:

    http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*

O parâmetro **searchFields** restringe a pesquisa apenas ao campo de título de negócios. O **queryType** é definido como **full**, que instrui o Azure Search a usar o Lucene Query Parser para essa consulta.

> [!NOTE]
> Para saber o contexto do processamento da consulta, consulte [Como funciona a pesquisa de texto completo no Azure Search](search-lucene-query-architecture.md). Para obter mais informações sobre parâmetros de pesquisa, consulte [Pesquisar documentos (API REST do serviço Azure Search)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).
>

### <a name="fielded-query-operation"></a>Operação de consulta por campo
Você pode modificar os exemplos deste artigo especificando uma construção **nomedocampo:termodepesquisa** para definir uma operação de consulta por campo, em que o campo é uma única palavra e o termo de pesquisa também é uma única palavra ou uma frase, opcionalmente com operadores boolianos. Alguns exemplos incluem o seguinte:

* business_title:(sênior NOT júnior)
* state:(“Nova York” AND “Nova Jersey”)

Coloque várias cadeias de caracteres entre aspas se quiser que ambas as cadeias de caracteres sejam avaliadas como uma única entidade, como neste caso, pesquisar duas cidades distintas no campo de local. Além disso, verifique se o operador está em maiúsculas, como você pode ver com NÃO e E.

O campo especificado em **nomedocampo:termodepesquisa** deve ser um campo pesquisável. Confira [Create Index (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) (Criar índice [API REST do Serviço Azure Search]) para obter detalhes sobre como os atributos de índice são usados em definições de campo.

**Exemplo 2** – clique com o botão direito do mouse no seguinte trecho de consulta Essa consulta pesquisa cargos executivos com o termo sênior, mas não júnior:

* [&queryType=full&search= business_title:senior NOT junior](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## <a name="fuzzy-search-example"></a>Exemplo de pesquisa difusa
Uma pesquisa difusa encontra correspondências em termos com uma construção semelhante. De acordo com a [documentação do Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), as pesquisas imprecisas se baseiam na [distância de Damerau-Levenshtein](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance).

Para fazer uma pesquisa difusa, acrescente o símbolo til “~” ao final de uma única palavra com um parâmetro opcional, um valor entre 0 e 2, que especifica a distância de edição. Por exemplo, "mar~" ou "mar~1" retornaria mar, amar e maré.

**Exemplo 3** : clique com o botão direito do mouse no trecho código de consulta a seguir. Essa consulta pesquisa os trabalhos com o termo associado (onde ele é escrito incorretamente):

* [&queryType=full&search= business_title:asosiate~](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

> [!Note]
> Consultas difusas não são [analisadas](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis), o que poderá ser uma surpresa se você esperar a lematização. A análise lexical é realizada somente em termos completos (uma consulta de termo ou de frase). Tipos de consulta com termos incompletos (consulta de prefixo, consulta de curinga, consulta de regex, consulta difusa) são adicionados diretamente à árvore de consulta, ignorando o estágio de análise. A única transformação realizada em termos de consulta incompletos é colocá-los em letras minúsculas.
>

## <a name="proximity-search-example"></a>Exemplo de pesquisa por proximidade
As pesquisas de proximidade são usadas para localizar termos que estejam próximos um do outro em um documento. Insira um símbolo til "~" no final de uma frase seguida pelo número de palavras que criam o limite de proximidade. Por exemplo, "hotel aeroporto"~5 encontrará os termos hotel e aeroporto em cinco palavras uma da outra em um documento.

**Exemplo 4** : clique com o botão direito do mouse na consulta. Procure trabalhos com o termo “analista sênior”, onde ele estiver separado por até uma palavra:

* [&queryType=full&search=business_title:"senior analyst"~1](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**Exemplo 5** : experimente novamente remover as palavras entre o termo "analista sênior".

* [&queryType=full&search=business_title:"senior analyst"~0](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## <a name="term-boosting-examples"></a>Exemplos de aumento de termo
O aumento de termos refere-se ao aumento da classificação de um documento caso ele contenha o termo aumentado, em relação aos documentos que não contêm o termo. Isso é diferente dos perfis de pontuação, já que os perfis de pontuação aumentam determinados campos, em vez de termos específicos. O exemplo a seguir ajuda a ilustrar as diferenças.

Considere um perfil de pontuação que aumente as correspondências em um determinado campo, como **gênero** no exemplo de índice de loja de música. O aumento de termos pode ser usado para melhorar a posição de determinados termos de pesquisa. Por exemplo, "rock^2 eletrônico" melhorará a posição dos documentos que contêm os termos de pesquisa no campo **gênero** , à frente de outros campos pesquisáveis no índice. Além disso, os documentos com o termo de pesquisa "rock" serão mais bem classificados do que o outro termo de pesquisa "eletrônico" como resultado do valor de aumento de termo (2).

Para aumentar um termo, use o sinal de interpolação, "^", com um fator de aumento (um número) no final do termo que você está pesquisando. Quanto maior o fator de aumento, mais relevante será o termo em relação a outros termos de pesquisa. Por padrão, o fator de aumento é 1. Embora o fator de aumento deva ser positivo, ele pode ser menor do que 1 (por exemplo, 0,2).

**Exemplo 6** - clique com o botão direito do mouse na consulta. Procure trabalhos com o termo "analista de computador", onde podemos ver que não há resultados com ambas as palavras, computador e analista, ainda que trabalhos de analista estejam na parte superior dos resultados.

* [&queryType=full&search=business_title:computer analyst](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**Exemplo 7** - tente novamente, desta vez aumentando os resultados com o termo computador em relação ao termo analista, caso ambas palavras não existam.

* [&queryType=full&search=business_title:computer^2 analyst](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## <a name="regular-expression-example"></a>Exemplo de expressão regular
Uma pesquisa de expressão regular encontra uma correspondência com base no conteúdo entre as barras "/", como documentado na [classe RegExp](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

**Exemplo 8** : clique com o botão direito do mouse na consulta. Procure trabalhos com o termo Sênior ou Júnior.

* `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

A URL para este exemplo não será renderizada corretamente na página. Como solução alternativa, copie a URL abaixo e cole-a no endereço de URL do navegador: `http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`

## <a name="wildcard-search-example"></a>Exemplo de pesquisa de curinga
Você pode usar a sintaxe geralmente reconhecida para pesquisas com vários caracteres curinga (\*) ou um caractere curinga (?). Observe que o analisador de consulta Lucene oferece suporte ao uso desses símbolos com um único termo e não uma frase.

**Exemplo 9** - clique com o botão direito do mouse na consulta. Procure os trabalhos que contenham o prefixo 'prog', que inclui cargos com os termos programação e o programador neles.

* [&queryType=full&$select=business_title&search=business_title:prog*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26queryType=full%26$select=business_title%26search=business_title:prog*)

Não é possível usar um símbolo * ou ? como o primeiro caractere de uma pesquisa.

## <a name="next-steps"></a>Próximas etapas
Tente especificar o Analisador de Consulta Lucene em seu código. Os links a seguir explicam como configurar consultas de pesquisa para o .NET e para a API REST. Os links usam a sintaxe simples padrão e, portanto, será necessário aplicar o que você aprendeu neste artigo para especificar o **queryType**.

* [Consultar seu índice do Azure Search usando o SDK do .NET](search-query-dotnet.md)
* [Consultar seu índice do Azure Search usando a API REST](search-query-rest-api.md)

## <a name="see-also"></a>Consulte também

 [Como funciona a pesquisa de texto completo no Azure Search](search-lucene-query-architecture.md)