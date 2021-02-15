---
title: Sintaxe de consulta simples
titleSuffix: Azure Cognitive Search
description: Referência para a sintaxe de consulta simples usada para consultas de pesquisa de texto completo no Azure Pesquisa Cognitiva.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: f679d6fbab57bcbcccc09b722f6b2f670df49eb2
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516589"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Sintaxe de consulta simples no Azure Pesquisa Cognitiva

O Azure Pesquisa Cognitiva implementa duas linguagens de consulta baseadas em Lucene: [analisador de consulta simples](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) e o [analisador de consulta Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). O analisador simples é mais flexível e tentará interpretar uma solicitação mesmo que não seja perfeitamente composta. Por causa dessa flexibilidade, é o padrão para consultas no Azure Pesquisa Cognitiva.

A sintaxe simples é usada para expressões de consulta passadas no **`search`** parâmetro de uma solicitação de [documentos de pesquisa (API REST)](/rest/api/searchservice/search-documents) , não deve ser confundida com a [sintaxe OData](query-odata-filter-orderby-syntax.md) usada para as [**`$filter`**](search-filters.md) [**`$orderby`**](search-query-odata-orderby.md) expressões e na mesma solicitação. Os parâmetros OData têm sintaxe e regras diferentes para construir consultas, cadeias de caracteres de escape e assim por diante.

Embora o analisador simples seja baseado na classe [analisador de consulta simples do Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) , a implementação no pesquisa cognitiva exclui a pesquisa difusa. Se você precisar de [pesquisa difusa](search-query-fuzzy.md), considere a [sintaxe de consulta Lucene completa](query-lucene-syntax.md) alternativa.

## <a name="example-simple-syntax"></a>Exemplo (sintaxe simples)

Embora **`queryType`** esteja definido abaixo, ele é o padrão e pode ser omitido, a menos que você esteja revertendo de um tipo alternativo. O exemplo a seguir é uma pesquisa em termos independentes, com um requisito de que todos os documentos correspondentes incluam "pool".

```http
POST https://{{service-name}}.search.windows.net/indexes/hotel-rooms-sample/docs/search?api-version=2020-06-30
{
  "queryType": "simple",
  "search": "budget hotel +pool",
  "searchMode": "all"
}
```

O **`searchMode`** parâmetro é relevante neste exemplo. Sempre que os operadores boolianos estão na consulta, você geralmente deve definir `searchMode=all` para garantir que *todos* os critérios sejam correspondidos. Caso contrário, você pode usar o padrão `searchMode=any` que favorece a RECALL sobre a precisão.

Para obter exemplos adicionais, consulte [exemplos de sintaxe de consulta simples](search-query-simple-examples.md). Para obter detalhes sobre a solicitação de consulta e os parâmetros, consulte [Pesquisar documentos (API REST)](/rest/api/searchservice/Search-Documents).

## <a name="keyword-search-on-terms-and-phrases"></a>Pesquisa de palavra-chave em termos e frases

As cadeias de caracteres passadas para o **`search`** parâmetro podem incluir termos ou frases em qualquer idioma com suporte, operadores boolianos, operadores de precedência, caracteres curinga ou prefixo para "começa com" consultas, caracteres de escape e caracteres de codificação de URL. O **`search`** parâmetro é opcional. Não especificado, Search ( `search=*` ou `search=" "` ) retorna os principais documentos de 50 em ordem arbitrária (não classificada).

+ Uma *pesquisa de termo* é uma consulta de um ou mais termos, em que qualquer um dos termos é considerado uma correspondência.

+ Uma *pesquisa de frase* é uma frase exata entre aspas `" "` . Por exemplo, while ```Roach Motel``` (sem aspas) pesquisaria documentos que contenham ```Roach``` e/ou em qualquer ```Motel``` lugar em qualquer ordem, ```"Roach Motel"``` (com aspas) só corresponderão a documentos que contenham essa frase inteira e nessa ordem (a análise léxica ainda se aplica). 

  Dependendo do seu cliente de pesquisa, talvez seja necessário escapar das aspas em uma pesquisa de frase. Por exemplo, no postmaster em uma solicitação POST, uma pesquisa de frase no `"Roach Motel"` corpo da solicitação seria especificada como `"\"Roach Motel\""` .

Por padrão, todos os termos ou frases passados no **`search`** parâmetro passam por análise léxica. Certifique-se de entender o comportamento de geração de tokens do analisador que você está usando. Geralmente, quando os resultados da consulta são inesperados, o motivo pode ser rastreado para como os termos são indexados no momento da consulta.

Qualquer texto com um ou mais termos é considerado um ponto de partida válido para a execução da consulta. O Azure Pesquisa Cognitiva corresponderá a documentos que contêm qualquer ou todos os termos, incluindo quaisquer variações encontradas durante a análise do texto.

Tão simples quanto esse som, há um aspecto da execução da consulta no Azure Pesquisa Cognitiva que *pode* produzir resultados inesperados, aumentando em vez de reduzir os resultados da pesquisa conforme mais termos e operadores são adicionados à cadeia de caracteres de entrada. Se essa expansão realmente ocorrer depende da inclusão de um operador NOT, combinada com uma **`searchMode`** configuração de parâmetro que determina como não é interpretado em termos de e ou ou comportamentos. Para obter mais informações, consulte o operador NOT em [operadores boolianos](#boolean-operators).

## <a name="boolean-operators"></a>Operadores boolianos

Você pode inserir operadores boolianos em uma cadeia de caracteres de consulta para melhorar a precisão de uma correspondência. Na sintaxe simples, os operadores boolianos são baseados em caracteres. Não há suporte para operadores de texto, como a palavra e,.

| Caractere | Exemplo | Uso |
|----------- |--------|-------|
| `+` | `pool + ocean` | Uma operação AND. Por exemplo, `pool + ocean` determina que um documento deve conter os dois termos.|
| `|` | `pool | ocean` | Uma operação ou encontra uma correspondência quando um dos termos é encontrado. No exemplo, o mecanismo de consulta retornará a correspondência em documentos que contenham um `pool` ou `ocean` ambos. Porque OR é o operador de conjunção padrão, que também pode ser deixado de fora, de modo que `pool ocean` é o equivalente de `pool | ocean`.|
| `-` | `pool – ocean` | Uma operação NOT retorna correspondências em documentos que excluem o termo. <br/><br/>Para obter o comportamento esperado em uma expressão NOT, considere **`searchMode=all`** a configuração na solicitação. Caso contrário, sob o padrão de **`searchMode=any`** , você obterá correspondências em `pool` , além de correspondências em todos os documentos que não contêm `ocean` , que podem ser muitos documentos. O **`searchMode`** parâmetro em uma solicitação de consulta controla se um termo com o operador NOT é ANDed ou orns com outros termos na consulta (supondo que não haja nenhum `+` `|` operador OR nos outros termos). **`searchMode=all`** O uso do aumenta a precisão das consultas, incluindo menos resultados e, por padrão, será interpretado como "e não". <br/><br/>Ao decidir sobre uma **`searchMode`** configuração, considere os padrões de interação do usuário para consultas em vários aplicativos. Os usuários que estão pesquisando informações têm mais probabilidade de incluir um operador em uma consulta, em oposição aos sites de comércio eletrônico que têm estruturas de navegação mais internas. |

<a name="prefix-search"></a>

## <a name="prefix-queries"></a>Consultas de prefixo

Para consultas "começa com", adicione um operador de sufixo ( `*` ) como o espaço reservado para o restante de um termo. Uma consulta de prefixo deve começar com pelo menos um caractere alfanumérico antes que você possa adicionar o operador de sufixo.

| Caractere | Exemplo | Uso |
|----------- |--------|-------|
| `*` | `lingui*` será correspondente em "linguístico" ou "Linguini" | O asterisco ( `*` ) representa um ou mais caracteres de comprimento arbitrário, ignorando maiúsculas e minúsculas.  |

Semelhante aos filtros, uma consulta de prefixo procura uma correspondência exata. Assim, não há nenhuma pontuação de relevância (todos os resultados recebem uma pontuação de pesquisa de 1,0). Lembre-se de que as consultas de prefixo podem ser lentas, especialmente se o índice for grande e o prefixo consistir em um pequeno número de caracteres. Uma metodologia alternativa, como a geração de tokens de n-grama de borda, pode ser executada mais rapidamente.

A sintaxe simples dá suporte apenas à correspondência de prefixo. Para a correspondência de sufixo ou infixo no final ou no meio de um termo, use a [sintaxe Lucene completa para pesquisa de curinga](query-lucene-syntax.md#bkmk_wildcard).

## <a name="escaping-search-operators"></a>Operadores de escape de pesquisa  

Na sintaxe simples, os operadores de pesquisa incluem estes caracteres: `+ | " ( ) ' \`  

Se qualquer um desses caracteres fizer parte de um token no índice, faça o escape dele prefixando-o com uma barra invertida única ( `\` ) na consulta. Por exemplo, suponha que você usou um analisador personalizado para geração de tokens de termo inteiro e o índice contém a cadeia de caracteres "luxo + Hotel". Para obter uma correspondência exata desse token, insira um caractere de escape: `search=luxury\+hotel` .

Para tornar as coisas simples para os casos mais comuns, há duas exceções a essa regra, em que o escape não é necessário:  

+ O operador NOT `-` só precisa ser ignorado se for o primeiro caractere após um espaço em branco. Se o `-` aparecer no meio (por exemplo, em `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD` ), você poderá ignorar a saída.

+ O operador de sufixo `*` só precisa ser ignorado se for o último caractere antes de um espaço em branco. Se o `*` aparecer no meio (por exemplo, em `4*4=16` ), nenhuma saída será necessária.

> [!NOTE]  
> Por padrão, o analisador padrão excluirá e quebrará palavras em hifens, espaço em branco, e comercial e outros caracteres durante a [análise léxica](search-lucene-query-architecture.md#stage-2-lexical-analysis). Se você precisar que caracteres especiais permaneçam na cadeia de caracteres de consulta, talvez seja necessário um analisador que os preserve no índice. Algumas opções incluem [analisadores de idioma](index-add-language-analyzers.md)natural da Microsoft, que preservam palavras hifenizadas ou um analisador personalizado para padrões mais complexos. Para obter mais informações, consulte [termos parciais, padrões e caracteres especiais](search-query-partial-matching.md).

## <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Codificação de caracteres reservados e não seguros em URLs

Verifique se todos os caracteres não seguros e reservados estão codificados em uma URL. Por exemplo, ' # ' é um caractere não seguro porque é um identificador de fragmento/âncora em uma URL. O caractere deverá ser codificado como `%23` se for usado em uma URL. ' & ' e ' = ' são exemplos de caracteres reservados à medida que delimitam parâmetros e especificam valores na Pesquisa Cognitiva do Azure. Para obter mais informações, consulte [RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt).

Os caracteres não seguros são ``" ` < > # % { } | \ ^ ~ [ ]``. Os caracteres reservados são `; / ? : @ = + &`.

## <a name="special-characters"></a>Caracteres especiais

Em algumas circunstâncias, talvez você queira procurar um caractere especial, como um Emoji ' ❤ ' ou o sinal ' € '. Nesses casos, certifique-se de que o analisador usado não filtre esses caracteres. O analisador padrão ignora muitos caracteres especiais, excluindo-os do índice.

Os analisadores que exportam caracteres especiais incluem o analisador "Whitespace", que leva em consideração quaisquer sequências de caracteres separadas por espaços em branco como tokens (portanto, a cadeia de caracteres "❤" seria considerada um token). Além disso, um analisador de linguagem como o Microsoft English Analyzer ("en. Microsoft"), levaria a cadeia de caracteres "€" como um token. Você pode [testar um analisador](/rest/api/searchservice/test-analyzer) para ver quais tokens ele gera para uma determinada consulta.

Ao usar caracteres Unicode, certifique-se de que os símbolos tenham um escape correto na URL de consulta (por exemplo, para "❤" usaria a sequência de escape `%E2%9D%A4+` ). O postmaster faz essa tradução automaticamente.  

## <a name="precedence-grouping"></a>Precedência (agrupamento)

Você pode usar parênteses para criar subconsultas, incluindo operadores dentro da instrução entre parênteses. Por exemplo, `motel+(wifi|luxury)` irá procurar documentos que contenham o termo "motel" e "wifi" ou "luxo" (ou ambos).

## <a name="query-size-limits"></a>Limites de tamanho de consulta

Se seu aplicativo gerar consultas de pesquisa por meio de programação, é recomendável criá-lo de forma que ele não gere consultas de tamanho ilimitado. 

+ Para GET, o comprimento da URL não pode exceder 8 KB.

+ Para POST (e qualquer outra solicitação), em que o corpo da solicitação inclui `search` e outros parâmetros, como `filter` e `orderby` , o tamanho máximo é 16 MB, em que o número máximo de cláusulas em `search` (expressões separadas por and, or e assim por diante) é 1024. Há também um limite de aproximadamente 32 KB em relação ao tamanho de qualquer termo individual em uma consulta. Para obter mais informações, consulte [limites de solicitação de API](search-limits-quotas-capacity.md#api-request-limits).

## <a name="next-steps"></a>Próximas etapas

Se você for construir consultas programaticamente, examine a [pesquisa de texto completo no Azure pesquisa cognitiva](search-lucene-query-architecture.md) para entender os estágios do processamento de consultas e as implicações da análise de texto.

Você também pode examinar os seguintes artigos para saber mais sobre a construção da consulta:

+ [Exemplos de consulta para pesquisa simples](search-query-simple-examples.md)
+ [Exemplos de consulta para a pesquisa completa do Lucene](search-query-lucene-examples.md)
+ [API REST para pesquisar documentos](/rest/api/searchservice/Search-Documents)
+ [Sintaxe de consulta Lucene](query-lucene-syntax.md)
+ [Sintaxe de expressão Filter e Select (OData)](query-odata-filter-orderby-syntax.md)