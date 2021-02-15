---
title: Usar sintaxe de consulta Lucene completa
titleSuffix: Azure Cognitive Search
description: Sintaxe de consulta Lucene para pesquisa difusa, pesquisa de proximidade, aumento de termo, pesquisa de expressão regular e pesquisas de curinga em um serviço de Pesquisa Cognitiva do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: Lucene query analyzer syntax
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: df26cfc3b220f40a7e73ff1c750d2b2ae37e7625
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401450"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Use a sintaxe de pesquisa "completa" do Lucene (consultas avançadas no Pesquisa Cognitiva do Azure)

Ao construir consultas para Pesquisa Cognitiva do Azure, você pode substituir o [analisador de consulta simples](query-simple-syntax.md) padrão pelo [analisador de consulta Lucene mais potente no Azure pesquisa cognitiva](query-lucene-syntax.md) para formular definições de consulta especializadas e avançadas. 

O analisador Lucene dá suporte a construções de consultas complexas, como consultas com escopo de campo, pesquisa difusa, pesquisa de curingas e sufixos, pesquisa de proximidade, aumento de termo e pesquisa de expressão regular. A energia adicional vem com requisitos adicionais de processamento, portanto, você deve esperar um tempo de execução um pouco mais longo. Neste artigo, você pode percorrer exemplos demonstrando operações de consulta com base na sintaxe completa.

> [!Note]
> Muitas construções de consulta especializadas habilitadas por meio da sintaxe de consulta Lucene completa não são [texto analisado](search-lucene-query-architecture.md#stage-2-lexical-analysis), o que pode ser surpreendente caso espera-se a lematização. A análise lexical é realizada somente em termos completos (uma consulta de termo ou de frase). Tipos de consulta com termos incompletos (consulta de prefixo, consulta de curinga, consulta de regex, consulta difusa) são adicionados diretamente à árvore de consulta, ignorando o estágio de análise. A única transformação executada em termos de consulta parcial está em letras minúsculas. 
>

## <a name="nyc-jobs-examples"></a>Exemplos de trabalhos do NYC

Os exemplos a seguir aproveitam o [índice de pesquisa de trabalhos do NYC](https://azjobsdemo.azurewebsites.net/)  que consiste em trabalhos disponíveis com base em um conjunto de uma [das cidades da iniciativa de OpenData de Nova York](https://nycopendata.socrata.com/). Esses dados não devem ser considerados atuais ou completos. O índice está em um serviço de área restrita fornecido pela Microsoft, o que significa que você não precisa de uma assinatura do Azure ou Pesquisa Cognitiva do Azure para tentar essas consultas.

O que você precisa é um postmaster ou uma ferramenta equivalente para emitir a solicitação HTTP em GET ou POST. Se você não estiver familiarizado com essas ferramentas, consulte [início rápido: explorar a API REST do Azure pesquisa cognitiva](search-get-started-rest.md).

## <a name="set-up-the-request"></a>Configurar a solicitação

1. Os cabeçalhos de solicitação devem ter os seguintes valores:

   | Chave | Valor |
   |-----|-------|
   | Tipo de conteúdo | `application/json`|
   | chave de API  | `252044BE3886FE4A8E3BAA4F595114BB` </br> (essa é a chave de API de consulta real para o serviço de pesquisa de área restrita que hospeda o índice de trabalhos NYC) |

1. Defina o verbo como **`GET`** .

1. Defina a URL como **`https://azs-playground.search.windows.net/indexes/nycjobs/docs/search=*&api-version=2020-06-30&queryType=full`**

   + A coleção de documentos no índice contém todo o conteúdo pesquisável. Uma chave de API de consulta fornecida no cabeçalho de solicitação só funciona para operações de leitura direcionando a coleção de documentos.

   + **`$count=true`** Retorna uma contagem dos documentos que correspondem aos critérios de pesquisa. Em uma cadeia de caracteres de pesquisa vazia, a contagem será todos os documentos no índice (cerca de 2558 no caso de trabalhos do NYC).

   + **`search=*`** é uma consulta não especificada, equivalente a nulo ou pesquisa vazia. Ele não é especialmente útil, mas é a pesquisa mais simples que você pode fazer e mostra todos os campos recuperáveis no índice, com todos os valores.

   + **`queryType=full`** invoca o analisador Lucene completo.

1. Como uma etapa de verificação, cole a solicitação a seguir no GET e clique em **Enviar**. Os resultados são retornados como documentos JSON detalhados.

   ```http
   https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*&queryType=full
   ```

### <a name="how-to-invoke-full-lucene-parsing"></a>Como invocar a análise completa do Lucene

Adicione **`queryType=full`** para invocar a sintaxe de consulta completa, substituindo a sintaxe de consulta simples padrão. Todos os exemplos neste artigo especificam o **`queryType=full`** parâmetro de pesquisa, indicando que a sintaxe completa é manipulada pelo analisador de consulta Lucene. 

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "queryType": "full"
}
```

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Exemplo 1: consulta com escopo para uma lista de campos

Este primeiro exemplo não é específico do analisador, mas nós o conduzimos para introduzir o primeiro conceito de consulta fundamental: contenção. Este exemplo limita a execução da consulta e a resposta a apenas alguns campos específicos. Saber como estruturar uma resposta JSON legível é importante quando sua ferramenta é Postman ou Search Explorer. 

Essa consulta destina-se somente a *business_title* no **`searchFields`** , especificando por meio do **`select`** parâmetro o mesmo campo na resposta.

```http
POST https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

A resposta para essa consulta deve ser semelhante à captura de tela a seguir.

  ![Resposta de exemplo do postmaster com pontuações](media/search-query-lucene-examples/postman-sample-results.png)

Você deve ter notado a pontuação de pesquisa na resposta. Pontuações uniformes de **1** ocorrem quando não há nenhuma classificação, porque a pesquisa não era uma pesquisa de texto completo ou porque nenhum critério foi fornecido. Para uma pesquisa vazia, as linhas retornam em ordem arbitrária. Quando você incluir critérios reais, verá as pontuações da pesquisa evoluir para valores significativos.

## <a name="example-2-fielded-search"></a>Exemplo 2: pesquisa em campo

A sintaxe Lucene completa dá suporte a expressões de pesquisa individuais de escopo para um campo específico. Este exemplo pesquisa títulos de negócios com o termo sênior neles, mas não Júnior. Você pode especificar vários campos usando e.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:(senior NOT junior) AND posting_type:external",
    "searchFields": "business_title, posting_type",
    "select": "business_title, posting_type"
}
```

A resposta para essa consulta deve ser semelhante à captura de tela a seguir (posting_type não é mostrado).

  :::image type="content" source="media/search-query-lucene-examples/intrafieldfilter.png" alt-text="Expressão de pesquisa de resposta de exemplo do postmaster" border="false":::

A expressão de pesquisa pode ser uma única palavra ou frase, ou uma expressão mais complexa entre parênteses, opcionalmente com operadores boolianos. Alguns exemplos incluem o seguinte:

+ `business_title:(senior NOT junior)`
+ `state:("New York" OR "New Jersey")`
+ `business_title:(senior NOT junior) AND posting_type:external`

Certifique-se de colocar várias cadeias de caracteres entre aspas se desejar que ambas as cadeias de caracteres sejam avaliadas como uma única entidade, como nesse caso procurando por dois locais distintos no `state` campo. Dependendo da ferramenta, talvez seja necessário escapar ( `\` ) as aspas. 

O campo especificado em **FieldName: searchId** deve ser um campo pesquisável. Consulte [criar índice (API REST do Azure pesquisa cognitiva)](/rest/api/searchservice/create-index) para obter detalhes sobre como os atributos de índice são usados em definições de campo.

> [!NOTE]
> No exemplo acima, o **`searchFields`** parâmetro é omitido porque cada parte da consulta tem um nome de campo especificado explicitamente. No entanto, você ainda pode usar **`searchFields`** se a consulta tiver várias partes (usando instruções and). Por exemplo, a consulta `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` corresponderia `senior NOT junior` apenas ao `business_title` campo, enquanto ele corresponderia a "external" com o `posting_type` campo. O nome do campo fornecido em `fieldName:searchExpression` sempre tem precedência sobre **`searchFields`** , que é o motivo neste exemplo, que podemos omitir `business_title` de **`searchFields`** .

## <a name="example-3-fuzzy-search"></a>Exemplo 3: pesquisa difusa

A sintaxe Lucene completa também dá suporte à pesquisa difusa, combinando em termos que têm uma construção semelhante. Para fazer uma pesquisa difusa, acrescente o símbolo til `~` ao final de uma única palavra com um parâmetro opcional, um valor entre 0 e 2, que especifica a distância de edição. Por exemplo, `blue~` ou `blue~1` retornariam mar, amar e maré.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:asosiate~",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Não há suporte diretamente para frases, mas você pode especificar uma correspondência difusa em cada termo de uma frase de várias partes, como `search=business_title:asosiate~ AND comm~` .  Na captura de tela abaixo, a resposta inclui uma correspondência no *associado à comunidade*.

  :::image type="content" source="media/search-query-lucene-examples/fuzzysearch.png" alt-text="Resposta de pesquisa difusa" border="false":::

> [!Note]
> As consultas difusas não são [analisadas](search-lucene-query-architecture.md#stage-2-lexical-analysis). Tipos de consulta com termos incompletos (consulta de prefixo, consulta de curinga, consulta de regex, consulta difusa) são adicionados diretamente à árvore de consulta, ignorando o estágio de análise. A única transformação executada em termos de consulta parcial está em letras minúsculas.
>

## <a name="example-4-proximity-search"></a>Exemplo 4: pesquisa por proximidade

As pesquisas de proximidade são usadas para localizar termos que estejam próximos um do outro em um documento. Insira um símbolo til "~" no final de uma frase seguida pelo número de palavras que criam o limite de proximidade. Por exemplo, "hotel aeroporto"~5 encontrará os termos hotel e aeroporto em cinco palavras uma da outra em um documento.

Essa consulta pesquisa os termos "sênior" e "analista", em que cada termo é separado por não mais de uma palavra, e as aspas são de escape ( `\"` ) para preservar a frase:

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:\"senior analyst\"~1",
    "searchFields": "business_title",
    "select": "business_title"
}
```

A resposta para essa consulta deve ser semelhante à captura de tela a seguir 

  :::image type="content" source="media/search-query-lucene-examples/proximity-before.png" alt-text="Consulta de proximidade" border="false":::

Tente novamente, eliminando qualquer distância ( `~0` ) entre os termos "analista sênior". Observe que 8 documentos são retornados para essa consulta, em oposição a 10 para a consulta anterior.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:\"senior analyst\"~0",
    "searchFields": "business_title",
    "select": "business_title"
}
```

## <a name="example-5-term-boosting"></a>Exemplo 5: aumento de termos

O aumento de termos refere-se ao aumento da classificação de um documento caso ele contenha o termo aumentado, em relação aos documentos que não contêm o termo. Para aumentar um termo, use o sinal de interpolação, o `^` símbolo com um fator de aumento (um número) no final do termo que você está pesquisando.

Nessa consulta para "antes", pesquise trabalhos com o termo *analista de computador* e observe que não há resultados com as palavras *computador* e *analista*, mas os trabalhos de *computador* estão no topo dos resultados.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:computer analyst",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Na consulta para "depois" repita a pesquisa, dessa vez, aumentando os resultados com o termo *analista* em relação ao termo *computador*, caso ambas as palavras não existam. É uma versão legível da consulta `search=business_title:computer analyst^2` . Para uma consulta viável no postmaster, `^2` é codificado como `%5E2` .

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:computer analyst%5e2",
    "searchFields": "business_title",
    "select": "business_title"
}
```

A resposta para essa consulta deve ser semelhante à captura de tela a seguir.

  :::image type="content" source="media/search-query-lucene-examples/termboostingafter.png" alt-text="Aumento depois de termos" border="false":::

O aumento de termos difere dos perfis de pontuação, em que os perfis de pontuação aumentam determinados campos, em vez de termos específicos. O exemplo a seguir ajuda a ilustrar as diferenças.

Considere um perfil de pontuação que aumente as correspondências em um determinado campo, como **gênero** no exemplo de índice de loja de música. O aumento de termos pode ser usado para melhorar a posição de determinados termos de pesquisa. Por exemplo, "rock^2 eletrônico" melhorará a posição dos documentos que contêm os termos de pesquisa no campo **gênero** , à frente de outros campos pesquisáveis no índice. Além disso, os documentos com o termo de pesquisa "rock" serão mais bem classificados do que o outro termo de pesquisa "eletrônico" como resultado do valor de aumento de termo (2).

Ao definir o nível do fator, quanto maior o fator de aumento, mais relevante será o termo em relação a outros termos de pesquisa. Por padrão, o fator de aumento é 1. Embora o fator de aumento deva ser positivo, ele pode ser menor do que 1 (por exemplo, 0,2).

## <a name="example-6-regex"></a>Exemplo 6: Regex

Uma pesquisa de expressão regular encontra uma correspondência com base no conteúdo entre as barras "/", como documentado na [classe RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:/(Sen|Jun)ior/",
    "searchFields": "business_title",
    "select": "business_title"
}
```

A resposta para essa consulta deve ser semelhante à captura de tela a seguir.

  :::image type="content" source="media/search-query-lucene-examples/regex.png" alt-text="Consulta de regex" border="false":::

> [!Note]
> Consultas Regex não são [analisadas](./search-lucene-query-architecture.md#stage-2-lexical-analysis). A única transformação executada em termos de consulta parcial está em letras minúsculas.
>

## <a name="example-7-wildcard-search"></a>Exemplo 7: pesquisa com curinga

Você pode usar a sintaxe geralmente reconhecida para pesquisas com vários caracteres curinga (\*) ou um caractere curinga (?). Observe que o analisador de consulta Lucene oferece suporte ao uso desses símbolos com um único termo e não uma frase.

Nessa consulta, pesquise trabalhos que contenham o prefixo 'prog', que incluiria títulos de negócios com os termos programação e programador. Você não pode usar `*` um `?` símbolo ou como o primeiro caractere de uma pesquisa.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:prog*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

A resposta para essa consulta deve ser semelhante à captura de tela a seguir.

  :::image type="content" source="media/search-query-lucene-examples/wildcard.png" alt-text="Consulta de caractere curinga" border="false":::

> [!Note]
> Consultas com curingas não são [analisadas](./search-lucene-query-architecture.md#stage-2-lexical-analysis). A única transformação executada em termos de consulta parcial está em letras minúsculas.
>

## <a name="next-steps"></a>Próximas etapas

Tente especificar consultas no código. Os links a seguir explicam como configurar consultas de pesquisa usando os SDKs do Azure.

+ [Consultar seu índice usando o SDK do .NET](search-get-started-dotnet.md)
+ [Consultar seu índice usando o SDK do Python](search-get-started-python.md)
+ [Consultar seu índice usando o SDK do JavaScript](search-get-started-javascript.md)

Referência de sintaxe adicional, arquitetura de consulta e exemplos podem ser encontrados nos links a seguir:

+ [Exemplos de consulta de sintaxe Lucene para criar consultas avançadas](search-query-lucene-examples.md)
+ [Como funciona a pesquisa de texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Sintaxe de consulta simples](query-simple-syntax.md)
+ [Sintaxe de consulta Lucene completa](query-lucene-syntax.md)
+ [Sintaxe de filtro](search-query-odata-filter.md)