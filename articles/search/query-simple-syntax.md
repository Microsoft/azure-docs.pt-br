---
title: Sintaxe de consulta simples - Azure Search
description: A referência para a sintaxe de consulta simples usada para consultas de pesquisa de texto completo no Azure Search.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/25/2019
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 99729141e5e1478f45ad385cf671c44a8e08f21a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61316890"
---
# <a name="simple-query-syntax-in-azure-search"></a>Sintaxe de consulta simples no Azure Search
O Azure Search implementa duas linguagens de consulta com base em Lucene: [Analisador de consulta simples](https://lucene.apache.org/core/4_7_0/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) e o [Analisador de Consulta do Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). No Azure Search, a sintaxe de consulta simples exclui as opções difusa/inclinada.  

> [!NOTE]  
>  O Azure Search fornece como alternativa a [sintaxe de consulta Lucene](query-lucene-syntax.md) para consultas mais complexas. Para saber mais sobre a arquitetura de análise de consultas e os benefícios de cada sintaxe, consulte [Como funciona a pesquisa de texto completo no Azure Search](search-lucene-query-architecture.md).

## <a name="how-to-invoke-simple-parsing"></a>Como invocar a análise de consulta simples

A sintaxe simples é o padrão. A invocação só será necessária se você redefinir a sintaxe de completa para simples. Para definir explicitamente a sintaxe, use o parâmetro de pesquisa `queryType`. Os valores válidos incluem `simple|full`, com `simple` como o padrão, e `full` para o Lucene. 

## <a name="query-behavior-anomalies"></a>Anomalias de comportamento de consulta

Qualquer texto com um ou mais termos é considerado um ponto de partida válido para a execução da consulta. O Azure Search corresponderá a documentos que contenham um ou todos os termos, incluindo qualquer variação encontrada durante a análise do texto. 

Por mais simples que pareça, há um aspecto da execução da consulta no Azure Search que *talvez* produza resultados inesperados, aumentando em vez de diminuir a pesquisa de resultados conforme mais termos e operadores são adicionados à cadeia de caracteres de entrada. Se essa expansão realmente ocorre depende da inclusão de um operador NOT, combinado a uma configuração de parâmetro `searchMode` que determina como NOT é interpretado em termos dos comportamentos de AND ou OR. Se você mantiver o padrão, `searchMode=Any`, e usar um operador NOT, a operação será computada como uma ação OR, de modo que `"New York" NOT Seattle` retornará todas as cidades que não sejam Seattle.  

O normal é que você veja mais desses comportamentos em padrões de interação do usuário com aplicativos que pesquisam conteúdo, em que os usuários têm maior probabilidade de incluir um operador em uma consulta, em vez de sites de comércio eletrônico, que têm mais estruturas internas de navegação. Para obter mais informações, confira [Operador NOT](#not-operator). 

## <a name="boolean-operators-and-or-not"></a>Operadores boolianos (AND, OR, NOT) 

Você pode inserir os operadores em uma cadeia de caracteres de consulta para criar um rico conjunto de critérios em relação ao qual os documentos correspondentes são encontrados. 

### <a name="and-operator-"></a>Operador AND `+`

O operador AND é um sinal de adição. Por exemplo, `wifi+luxury` procura documentos que contenham `wifi` e `luxury`.

### <a name="or-operator-"></a>Operador OR `|`

O operador OR é uma barra vertical ou um caractere de pipe. Por exemplo, `wifi | luxury` procura documentos que contenham `wifi`, `luxury` ou ambos.

<a name="not-operator"></a>

### <a name="not-operator--"></a>Operador NOT `-`

O operador NOT é um sinal de subtração. Por exemplo, `wifi –luxury` irá procurar documentos que tenham o termo `wifi` e/ou não tenham `luxury` (and/or são controlador por `searchMode`).

> [!NOTE]  
>  A opção `searchMode` controla se um termo com o operador NOT tem AND ou OR com outros termos da consulta na ausência de um operador `+` ou `|`. Lembre-se de que `searchMode` pode ser definido como `any` (padrão) ou `all`. Usar `any` aumenta a recuperação de consultas, incluindo mais resultados e, por padrão, `-` será interpretado como "OR NOT". Por exemplo, `wifi -luxury` corresponderá a documentos que contêm o termo `wifi` ou aqueles que não contêm o termo `luxury`. Usar `all` aumenta a precisão de consultas por incluir menos resultados e, por padrão, será interpretado como "AND NOT". Por exemplo, `wifi -luxury` corresponderá a documentos que contêm o termo `wifi` e não contêm o termo "luxo". Esse é indiscutivelmente um comportamento mais intuitivo para o operador `-`. Portanto, considere escolher `searchMode=all` em vez de `searchMode=any` se quiser otimizar pesquisas por precisão em vez de recuperação e, *além disso*, seus usuários frequentemente usam o operador `-` nas pesquisas.

## <a name="suffix-operator"></a>Operador de sufixo

O operador de sufixo é um asterisco `*`. Por exemplo, `lux*` procura documentos que tenham um termo que começa com `lux`, ignorando maiúsculas e minúsculas.  

## <a name="phrase-search-operator"></a>Operador de pesquisa de frase

O operador de expressão inclui uma frase aspas `" "`. Por exemplo, embora `Roach Motel` (sem aspas) procuraria documentos que contenham `Roach` e/ou `Motel` em qualquer lugar e em qualquer ordem, `"Roach Motel"` (com aspas) faz a correspondência apenas com documentos que contenham essa frase inteira, junta e naquela ordem (a análise de texto ainda se aplica).

## <a name="precedence-operator"></a>Operador de precedência

O operador de precedência inclui a cadeia de caracteres entre parênteses `( )`. Por exemplo, `motel+(wifi | luxury)` irá procurar documentos que contenham o termo motel e uma `wifi` ou `luxury` (ou ambos).  

## <a name="escaping-search-operators"></a>Operadores de escape de pesquisa  

 Para usar os símbolos acima como parte real do texto da pesquisa, eles devem ter como prefixo uma barra invertida para serem ignorados na pesquisa. Por exemplo, `luxury\+hotel` resultará no termo `luxury+hotel`. Para simplificar as coisas para os casos mais comuns, há duas exceções a essa regra em que o escape não é necessário:  

- O operador NOT `-` só precisará ser ignorado se for o primeiro caractere após o espaço em branco, não se ele estiver no meio de um termo. Por exemplo, `wi-fi` é um termo único; enquanto GUIDs (como `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`) são tratados como um único token.
- O operador de sufixo `*` só precisa ser ignorado se for o último caractere após o espaço em branco, não se ele estiver no meio de um termo. Por exemplo, `wi*fi` é tratado como um único token.

> [!NOTE]  
>  Embora o escape mantenha os tokens juntos, a análise de texto pode dividi-los, dependendo do modo de análise. Confira [Suporte de linguagem da &#40;API REST do Serviço do Azure Search&#41;](index-add-language-analyzers.md) para saber mais.  

## <a name="see-also"></a>Consulte também  

+ [Pesquisar documentos &#40;API REST do Serviço do Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Sintaxe de consulta Lucene](query-lucene-syntax.md)
+ [Sintaxe de expressão do OData](query-odata-filter-orderby-syntax.md) 
