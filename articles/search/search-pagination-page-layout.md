---
title: Como trabalhar com os resultados da pesquisa
titleSuffix: Azure Cognitive Search
description: Estruture e classifique os resultados da pesquisa, obtenha uma contagem de documentos e adicione navegação de conteúdo para resultados no Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 92db62622c37241a76d7847931df030162de8f00
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504219"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>Como trabalhar com os resultados da pesquisa no Azure Cognitive Search

Este artigo explica como formular uma resposta de consulta no Azure Cognitive Search. A estrutura da resposta é determinada pelos parâmetros na consulta: [documento de pesquisa](/rest/api/searchservice/Search-Documents) na API REST ou [classe SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1) no SDK do .NET. Os parâmetros na consulta podem ser usados para estruturar o conjunto de resultados das seguintes maneiras:

+ Limitar ou enviar em lote o número de documentos nos resultados (50 por padrão)
+ Selecionar campos para incluir nos resultados
+ Ordenar resultados
+ Realçar um termo inteiro ou parcial correspondente no corpo dos resultados da pesquisa

## <a name="result-composition"></a>Composição do resultado

Embora um documento de pesquisa possa consistir em um grande número de campos, normalmente apenas alguns são necessários para representar cada documento no conjunto de resultados. Em uma solicitação de consulta, acrescente `$select=<field list>` para especificar quais campos aparecem na resposta. Um campo deve ter a atribuição **recuperável** no índice para ser incluído no resultado. 

Os campos que funcionam melhor incluem aqueles que contrastam e diferenciam entre documentos, mostrando informações suficientes para incentivar o usuário a clicar na resposta. Em um site de comércio eletrônico, pode ser um nome de produto, uma descrição, uma marca, uma cor, um tamanho, um preço e uma classificação. No exemplo interno hotels-sample-index, podem ser campos no exemplo a seguir:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> Para incluir arquivos de imagem no resultado, como uma foto do produto ou um logotipo, armazene os arquivos fora do Azure Cognitive Search, mas inclua um campo no índice que faça referência à URL da imagem no documento de pesquisa. Os índices de amostra que dão suporte para imagens nos resultados são a demonstração **realestate-sample-us**, apresentada neste [guia de início rápido](search-create-app-portal.md), e o [aplicativo de demonstração de trabalhos da cidade de Nova York](https://aka.ms/azjobsdemo).

### <a name="tips-for-unexpected-results"></a>Dicas para resultados inesperados

Ocasionalmente, a substância e não a estrutura dos resultados são inesperadas. Quando os resultados da consulta forem inesperados, use estas modificações de consulta para verificar se os resultados melhoram:

+ Altere **`searchMode=any`** (padrão) para **`searchMode=all`** para exigir correspondências em todos os critérios, em vez de em qualquer um dos critérios. Isso é especialmente verdadeiro quando os operadores boolianos estão incluídos na consulta.

+ Use diferentes analisadores léxicos ou personalizados para tentar alterar o resultado da consulta. O analisador padrão quebra as palavras hifenizadas e reduz as palavras à raiz, o que geralmente melhora a robustez da resposta de consulta. No entanto, caso você precise preservar hifens ou caso as cadeias de caracteres incluam caracteres especiais, talvez seja necessário configurar analisadores personalizados para garantir que o índice contenha tokens no formato correto. Veja mais informações em [Pesquisa de termo parcial e padrões com caracteres especiais (hifens, curinga, regex, padrões)](search-query-partial-matching.md).

## <a name="paging-results"></a>Resultados da paginação

Por padrão, o mecanismo de pesquisa retorna até as primeiras 50 correspondências, na ordem determinada pela pontuação em pesquisas de texto completo ou em ordem arbitrária nas consultas de correspondência exata.

Para retornar um número diferente de documentos correspondentes, adicione os parâmetros `$top` e `$skip` à solicitação de consulta. A lista a seguir explica a lógica.

+ Adicione `$count=true` para obter uma contagem do número total de documentos correspondentes em um índice.

+ Retornar o primeiro conjunto de 15 documentos correspondentes mais uma contagem do total de correspondências: `GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`

+ Retornar o segundo conjunto, ignorando os 15 primeiros para obter os próximos 15: `$top=15&$skip=15`. Fazer o mesmo para o terceiro conjunto de 15: `$top=15&$skip=30`

Se há mudanças no índice subjacente, talvez os resultados de consultas paginadas não sejam estáveis. A paginação altera o valor de `$skip` de cada página, mas cada consulta é independente e opera na exibição atual dos dados que existe no índice no momento da consulta. Em outras palavras, não há cache ou instantâneo dos resultados, como existem em bancos de dados de uso geral.
 
Veja a seguir um exemplo que mostra como duplicatas podem surgir. Imagine um índice com quatro documentos:

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
{ "id": "4", "rating": 1 }
```
 
Agora imagine que você quer dois resultados por vez, ordenados pela classificação. Você executaria esta consulta para obter a primeira página de resultados: `$top=2&$skip=0&$orderby=rating desc`, produzindo os seguintes resultados:

```text
{ "id": "1", "rating": 5 }
{ "id": "2", "rating": 3 }
```
 
No serviço, suponha que um quinto documento seja adicionado ao índice entre as chamadas de consulta: `{ "id": "5", "rating": 4 }`.  Logo em seguida, você executa uma consulta para buscar a segunda página, `$top=2&$skip=2&$orderby=rating desc`, e este é o resultado:

```text
{ "id": "2", "rating": 3 }
{ "id": "3", "rating": 2 }
```
 
Observe que o documento 2 é recuperado duas vezes. Isso ocorre porque o novo documento 5 tem um valor de classificação maior, então é classificado antes do documento 2 e chega na primeira página. Embora possa ser inesperado, esse é o comportamento comum do mecanismo de pesquisa.

## <a name="ordering-results"></a>Ordenando resultados

Para consultas de pesquisa de texto completo, os resultados são classificados automaticamente por uma pontuação de pesquisa, calculada com base na frequência do termo e na proximidade em um documento (derivada de [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)). As pontuações mais altas são atribuídas a documentos com mais correspondências ou correspondências mais fortes de um termo de pesquisa. 

As pontuações de pesquisa indicam a relevância geral, refletindo o grau da correspondência em relação a outros documentos no mesmo conjunto de resultados. Mas as pontuações nem sempre são consistentes de uma consulta para a outra, assim, à medida que você trabalha com consultas, pode notar pequenas discrepâncias na ordenação dos documentos de pesquisa. Há várias explicações para isso.

| Causa | Descrição |
|-----------|-------------|
| Volatilidade de dados | O conteúdo do índice varia quando você adiciona, modifica ou exclui documentos. As frequências de termo mudam conforme as atualizações de índice são processadas ao longo do tempo, afetando as pontuações de pesquisa de documentos correspondentes. |
| Várias réplicas | Para serviços que usam várias réplicas, as consultas são emitidas em cada réplica em paralelo. As estatísticas de índice usadas para calcular uma pontuação de pesquisa são calculadas por réplica, com os resultados mesclados e ordenados na resposta da consulta. As réplicas são, na maioria, espelhos umas das outras, mas as estatísticas podem ser diferentes devido a pequenas diferenças no estado. Por exemplo, é possível que documentos excluídos entrem nas estatísticas de uma réplica, mas sejam eliminados pela mesclagem em outras réplicas. Normalmente, as diferenças nas estatísticas por réplica são mais perceptíveis em índices menores. |
| Pontuações idênticas | Quando vários documentos têm a mesma pontuação, qualquer um deles pode aparecer primeiro.  |

### <a name="how-to-get-consistent-ordering"></a>Como obter uma ordem consistente

Para aplicativos que exigem ordenação consistente, você pode definir explicitamente uma [**expressão `$orderby`** ](query-odata-filter-orderby-syntax.md) em um campo. Somente os campos que são indexados como **`sortable`** podem ser usados para ordenar os resultados. Alguns campos geralmente usados em **`$orderby`** incluem classificação, data e localização de inclusão, caso você especifique o valor do parâmetro **`orderby`** para incluir nomes de campos e chamadas à [**função `geo.distance()`** ](query-odata-filter-orderby-syntax.md) para valores geoespaciais.

Outra abordagem que promove a consistência é usar um [perfil de pontuação personalizado](index-add-scoring-profiles.md). Os perfis de pontuação oferecem maior controle sobre a classificação de itens nos resultados da pesquisa, com a capacidade de impulsionar as correspondências encontradas em campos específicos. A lógica de pontuação adicional ajuda a substituir pequenas diferenças entre as réplicas, pois as pontuações de pesquisa de cada documento estão mais distantes. Recomendamos o [algoritmo de classificação](index-ranking-similarity.md) para essa abordagem.

## <a name="hit-highlighting"></a>Realce de ocorrência

O realce de ocorrência é a formatação de texto (como negrito ou realce em amarelo) aplicada aos termos correspondentes do resultado, facilitando a identificação da correspondência. As instruções de realce de ocorrência são incluídas na [solicitação de consulta](/rest/api/searchservice/search-documents). 

Para habilitar o recurso, adicione `highlight=[comma-delimited list of string fields]` para especificar quais campos usarão o realce. O realce é útil para campos de conteúdo mais longo, como uma descrição, em que a correspondência não é imediatamente óbvia. É possível usar o recurso apenas nas definições de campo com o atributo **pesquisável**.

Por padrão, o Azure Cognitive Search retorna até cinco destaques por campo. Para ajustar esse número, acrescente ao campo um traço seguido por um inteiro. Por exemplo, `highlight=Description-10` retorna até 10 destaques no conteúdo correspondente no campo Descrição.

A formatação é aplicada a consultas de termo completo. O tipo de formatação é determinado pelas marcas `highlightPreTag` `highlightPostTag`, e o código cuida da resposta (por exemplo, aplicar uma fonte em negrito ou uma tela de fundo amarela).

No exemplo a seguir, os termos "arenoso", "areia", "praias" e "praia", presentes no campo Descrição, são marcados para realce. As consultas que disparam expansão no mecanismo, como pesquisa difusa e curinga, têm suporte limitado para o realce de ocorrência.

```http
GET /indexes/hotels-sample-index/docs/search=sandy beaches&highlight=Description?api-version=2020-06-30 
```

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30 
    {  
      "search": "sandy beaches",  
      "highlight": "Description"
    }
```

### <a name="new-behavior-starting-july-15"></a>Novo comportamento (desde 15 de julho)

Os serviços criados após 15 de julho de 2020 têm uma experiência de realce diferente. Os serviços criados antes dessa data continuam com o mesmo comportamento de realce. 

Com o novo comportamento:

+ Somente frases que correspondam à consulta de frase completa serão retornadas. A frase de consulta "super bowl" retorna destaques como este:

  ```json
  "@search.highlights": {
      "sentence": [
          "The <em>super</em> <em>bowl</em> is super awesome with a bowl of chips"
     ]
  ```

  Outras instâncias de *super* e *bowl* não têm realce porque não correspondem à frase completa.

Lembre-se dessa alteração ao criar código de cliente que implemente o realce de ocorrência. Observe que isso não afetará você, a menos que você crie um serviço de pesquisa completamente novo.

## <a name="next-steps"></a>Próximas etapas

Para gerar rapidamente uma página de pesquisa para o cliente, considere estas opções:

+ O [gerador de aplicativos](search-create-app-portal.md), no portal, cria uma página HTML com uma barra de pesquisa, faceted navigation e área de resultados que inclui imagens.
+ [Crie seu primeiro aplicativo em C#](tutorial-csharp-create-first-app.md) é um tutorial que cria um cliente funcional. O exemplo de código demonstra consultas paginadas, realce de ocorrência e classificação.

Vários exemplos de código incluem uma interface de front-end da Web, disponível aqui: [aplicativo de demonstração de trabalhos da cidade de Nova York](https://aka.ms/azjobsdemo), [código de exemplo de JavaScript com um site de demonstração ao vivo](https://github.com/liamca/azure-search-javascript-samples) e [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).
