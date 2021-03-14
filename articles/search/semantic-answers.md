---
title: Retornar uma resposta semântica
titleSuffix: Azure Cognitive Search
description: Descreve a composição de uma resposta semântica e como obter respostas de um conjunto de resultados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: b99cbf91d7fc1c5d90753dfa1461a58eda055180
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418888"
---
# <a name="return-a-semantic-answer-in-azure-cognitive-search"></a>Retornar uma resposta semântica no Azure Pesquisa Cognitiva

> [!IMPORTANT]
> Os recursos de pesquisa semântica estão em visualização pública, disponíveis somente por meio da API REST de visualização. Os recursos de visualização são oferecidos no estado em que se encontram, sob [termos de uso suplementares](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)e não têm a garantia de ter a mesma implementação em disponibilidade geral. Para obter mais informações, consulte [disponibilidade e preços](semantic-search-overview.md#availability-and-pricing).

Ao formular uma [consulta semântica](semantic-how-to-query-request.md), você pode, opcionalmente, extrair o conteúdo dos documentos de correspondência superior que "responde" diretamente à consulta. Uma ou mais respostas podem ser incluídas na resposta, que você pode renderizar em uma página de pesquisa para melhorar a experiência do usuário do seu aplicativo.

Neste artigo, saiba como solicitar uma resposta semântica, descompactar a resposta e saber quais características de conteúdo são mais úteis para produzir respostas de alta qualidade.

## <a name="prerequisites"></a>Pré-requisitos

Todos os pré-requisitos que se aplicam a [consultas semânticas](semantic-how-to-query-request.md) também se aplicam a respostas, incluindo a camada de serviço e a região.

+ Consultas formuladas usando os parâmetros de consulta semântica e incluem o parâmetro "Answers". Os parâmetros necessários são discutidos neste artigo.

+ As cadeias de caracteres de consulta devem ser formuladas em linguagem com as características de uma pergunta (o que, onde, quando, como).

+ Os documentos de pesquisa devem conter texto com as características de uma resposta e esse texto deve existir em um dos campos listados em "searchFields".

## <a name="what-is-a-semantic-answer"></a>O que é uma resposta semântica?

Uma resposta semântica é um artefato de uma [consulta semântica](semantic-how-to-query-request.md). Ele consiste em uma ou mais passagens idênticas de um documento de pesquisa, formulado como uma resposta a uma consulta que se parece com uma pergunta. Para que uma resposta seja retornada, frases ou sentenças devem existir em um documento de pesquisa que tenha as características da linguagem de uma resposta, e a consulta em si deve ser apresentada como uma pergunta.

Pesquisa Cognitiva usa um modelo de compreensão de leitura de máquina para formular respostas. O modelo produz um conjunto de possíveis respostas dos documentos disponíveis e, quando atinge um nível de confiança alto o suficiente, ele propõe uma resposta.

As respostas são retornadas como um objeto independente e de nível superior na carga de resposta de consulta que você pode escolher para renderizar em páginas de pesquisa, além dos resultados da pesquisa lateral. Estruturalmente, é um elemento de matriz de uma resposta que inclui texto, uma chave de documento e uma pontuação de confiança.

<a name="query-params"></a>

## <a name="how-to-request-semantic-answers-in-a-query"></a>Como solicitar respostas semânticas em uma consulta

Para retornar uma resposta semântica, a consulta deve ter o tipo de consulta semântica, idioma, campos de pesquisa e o parâmetro "respostas". A especificação do parâmetro "Answers" não garante que você receberá uma resposta, mas a solicitação deverá incluir esse parâmetro se o processamento de resposta for ser invocado.

O parâmetro "searchFields" é essencial para retornar uma resposta de alta qualidade, tanto em termos de conteúdo quanto de ordem. 

```json
{
    "search": "how do clouds form",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "title,locations,content",
    "answers": "extractive|count-3",
    "count": "true"
}
```

+ Uma cadeia de caracteres de consulta não deve ser nula e deve ser formulada como uma pergunta. Nesta visualização, o "QueryType" e "queryLanguage" devem ser definidos exatamente como mostrado no exemplo.

+ O parâmetro "searchFields" determina quais campos fornecem tokens para o modelo de extração. Certifique-se de definir esse parâmetro. Você deve ter pelo menos um campo de cadeia de caracteres, mas inclua qualquer campo de cadeia de caracteres que você ache útil ao fornecer uma resposta. Apenas cerca de 8.000 tokens por documento são passados para o modelo. Inicie a lista de campos com campos concisos e, em seguida, progresso para campos Rich Text. Para obter orientações precisas sobre como definir esse campo, consulte [set searchFields](semantic-how-to-query-request.md#searchfields).

+ Para "respostas", a construção do parâmetro básico é `"answers": "extractive"` , em que o número padrão de respostas retornadas é um. Você pode aumentar o número de respostas adicionando uma contagem, até um máximo de cinco.  A necessidade de mais de uma resposta depende da experiência do usuário do seu aplicativo e de como você deseja renderizar os resultados.

## <a name="deconstruct-an-answer-from-the-response"></a>Desconstruir uma resposta da resposta

As respostas são fornecidas na @search.answers matriz, que aparece primeiro na resposta. Se uma resposta for indeterminada, a resposta será exibida como `"@search.answers": []` . Ao criar uma página de resultados da pesquisa que inclua respostas, certifique-se de lidar com casos em que as respostas não foram encontradas.

Em @search.answers , a "chave" é a chave do documento ou a ID da correspondência. Dada uma chave de documento, você pode usar a API de [documento de pesquisa](/rest/api/searchservice/lookup-document) para recuperar uma ou todas as partes do documento de pesquisa a serem incluídas na página de pesquisa ou em uma página de detalhes.

"Texto" e "Destaques" fornecem conteúdo idêntico, em texto sem formatação e com destaques. Por padrão, os destaques são estilizados como `<em>` , que podem ser substituídos usando os parâmetros highlightPreTag e highlightPostTag existentes. Conforme observado em outro lugar, a substância de uma resposta é o conteúdo textual de um documento de pesquisa. O modelo de extração procura características de uma resposta para encontrar o conteúdo apropriado, mas não compõe o novo idioma na resposta.

A "pontuação" é uma pontuação de confiança que reflete a força da resposta. Se houver várias respostas na resposta, essa pontuação será usada para determinar a ordem. As principais respostas e as legendas superiores podem ser derivadas de diferentes documentos de pesquisa, onde a resposta principal se origina de um documento e a legenda superior de outro, mas em geral você verá os mesmos documentos nas posições principais dentro de cada matriz.

As respostas são seguidas pela matriz "value", que sempre inclui pontuações, legendas e todos os campos que são recuperáveis por padrão. Se você tiver especificado o parâmetro Select, a matriz "value" será limitada aos campos que você especificou. Para obter mais informações sobre itens na resposta, consulte [criar uma consulta semântica](semantic-how-to-query-request.md).

Dada a consulta "como fazer nuvens Form", a seguinte resposta é retornada na resposta:

```json
{
    "@search.answers": [
        {
            "key": "4123",
            "text": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form where air is ascending (over land in this case),   but not where it is descending (over the river).",
            "highlights": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form<em> where air is ascending</em> (over land in this case),   but not where it is<em> descending</em> (over the river).",
            "score": 0.94639826
        }
    ],
    "value": [
        {
            "@search.score": 0.5479723,
            "@search.rerankerScore": 1.0321671911515296,
            "@search.captions": [
                {
                    "text": "Like all clouds, it forms when the air reaches its dew point—the temperature at which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley fog, which is common in the Pacific Northwest of North America.",
                    "highlights": "Like all<em> clouds</em>, it<em> forms</em> when the air reaches its dew point—the temperature at    which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley<em> fog</em>, which is common in the Pacific Northwest of North America."
                }
            ],
            "title": "Earth Atmosphere",
            "content": "Fog is essentially a cloud lying on the ground. Like all clouds, it forms when the air reaches its dew point—the temperature at  \n\nwhich an air mass is cool enough for its water vapor to condense into liquid droplets.\n\nThis false-color image shows valley fog, which is common in the Pacific Northwest of North America. On clear winter nights, the \n\nground and overlying air cool off rapidly, especially at high elevations. Cold air is denser than warm air, and it sinks down into the \n\nvalleys. The moist air in the valleys gets chilled to its dew point, and fog forms. If undisturbed by winds, such fog may persist for \n\ndays. The Terra satellite captured this image of foggy valleys northeast of Vancouver in February 2010.\n\n\n",
            "locations": [
                "Pacific Northwest",
                "North America",
                "Vancouver"
            ]
        }
```

## <a name="tips-for-producing-high-quality-answers"></a>Dicas para produzir respostas de alta qualidade

Para obter melhores resultados, retorne as respostas semânticas em um documento corpus com as seguintes características:

+ "searchFields" deve incluir um ou mais campos que forneçam texto suficiente no qual uma resposta provavelmente será encontrada.

+ A extração semântica e o resumo têm limites sobre a quantidade de conteúdo que pode ser analisada em tempo hábil. Coletivamente, apenas os primeiros 20.000 tokens são analisados. Qualquer coisa além disso será ignorada. Em termos práticos, se você tiver documentos grandes que são executados em centenas de páginas, deverá tentar dividir o conteúdo em partes gerenciáveis primeiro.

+ as cadeias de consulta não devem ser nulas (Search = `*` ) e a cadeia de caracteres deve ter as características de uma pergunta, em oposição a uma pesquisa de palavra-chave (uma lista seqüencial de termos ou frases arbitrárias). Se a cadeia de caracteres de consulta não parece ser uma resposta, o processamento de resposta é ignorado, mesmo que a solicitação especifique "respostas" como um parâmetro de consulta.

## <a name="next-steps"></a>Próximas etapas

+ [Visão geral da pesquisa semântica](semantic-search-overview.md)
+ [Algoritmo de classificação semântica](semantic-ranking.md)
+ [Algoritmo de similaridade](index-ranking-similarity.md)
+ [Criar uma consulta semântica](semantic-how-to-query-request.md)