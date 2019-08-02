---
title: Indexação de vários idiomas para consultas de pesquisa em idiomas diferentes do inglês – Azure Search
description: O Azure Search dá suporte a 56 idiomas, aproveitando os analisadores de linguagem da Lucene e a tecnologia Processamento de Linguagem Natural da Microsoft.
author: yahnoosh
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: jlembicz
ms.custom: seodec2018
ms.openlocfilehash: 403febfcb54194602051aaebe2952265c0675e9d
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854407"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-search"></a>Como criar um índice para vários idiomas no Azure Search

Os índices podem incluir campos que contenham conteúdo de vários idiomas, por exemplo, criando campos individuais para cadeias de caracteres específicas do idioma. Para obter melhores resultados durante a indexação e a consulta, atribua um analisador de linguagem que fornece as regras linguísticas apropriadas. 

O Azure Search oferece uma grande seleção de analisadores de linguagem tanto do Lucene quanto da Microsoft que podem ser atribuídos a campos individuais usando a propriedade do analisador. Você também pode especificar um analisador de idioma no portal, conforme descrito neste artigo.

## <a name="add-analyzers-to-fields"></a>Adicionar analisadores a campos

Um analisador de idioma é especificado quando um campo é criado. A adição de um analisador a uma definição de campo existente requer a substituição (e o recarregamento) do índice ou a criação de um novo campo idêntico ao original, mas com uma atribuição de analisador. Em seguida, você pode excluir o campo não utilizado de sua conveniência.

1. Entre no [portal do Azure](https://portal.azure.com) e localize o serviço de pesquisa.
1. Clique em **Adicionar índice** na barra de comandos localizada na parte superior do painel do serviço para iniciar um novo índice, ou abra um índice existente para definir um analisador nos novos campos que você está adicionando a um índice existente.
1. Inicie uma definição de campo fornecendo um nome.
1. Escolha o tipo de dados EDM. String. Somente os campos de cadeia de caracteres são pesquisáveis com texto completo.
1. Defina o  atributo pesquisável para habilitar a propriedade do analisador. Um campo deve ser baseado em texto para fazer uso de um analisador de idioma.
1. Escolha um dos analisadores disponíveis. 

![Atribuir analisadores de idioma durante a definição do campo](media/search-language-support/select-analyzer.png "Atribuir analisadores de idioma durante a definição do campo")

Por padrão, todos os campos pesquisáveis usam o [analisador Lucene padrão](https://lucene.apache.org/core/4_10_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) , que é independente de linguagem. Para exibir a lista completa de analisadores com suporte, consulte [Adicionar analisadores de idioma a um índice de Azure Search](index-add-language-analyzers.md).

No portal, os analisadores se destinam a serem usados no estado em que se encontram. Se você precisar de personalização ou de uma configuração específica de filtros e criadores, deverá [criar um analisador personalizado](index-add-custom-analyzers.md) no código. O portal não dá suporte à seleção ou configuração de analisadores personalizados.

## <a name="query-language-specific-fields"></a>Campos específicos da linguagem de consulta

Quando o analisador de linguagem for selecionado para um campo, ele será usado com cada solicitação de indexação e pesquisa para esse campo. Quando uma consulta é emitida em vários campos usando analisadores diferentes, a consulta será processada independentemente pelos analisadores atribuídos para cada campo.

Se o idioma do agente emissor de uma consulta for conhecido, uma solicitação de pesquisa pode ser definida como escopo para um campo específico usando o parâmetro de consulta **searchFields** . A seguinte consulta será emitida apenas com a descrição em polonês:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=PolishContent&api-version=2019-05-06`

Você pode consultar o índice no portal, usando o [**Search Explorer**](search-explorer.md) para colar uma consulta semelhante à mostrada acima.

## <a name="boost-language-specific-fields"></a>Impulsionar campos específicos do idioma

Às vezes, o idioma do agente emissor de uma consulta não é conhecido; nesse caso, a consulta pode ser emitida em todos os campos simultaneamente. Se necessário, a preferência de resultados em um determinado idioma pode ser definida usando os [perfis de pontuação](index-add-scoring-profiles.md). No exemplo abaixo, as correspondências encontradas na descrição em inglês terão uma pontuação superior em relação às correspondências em polonês e francês:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2019-05-06`

## <a name="next-steps"></a>Próximas etapas

Se você for um desenvolvedor do .NET, observe que é possível configurar analisadores de idioma usando o [SDK do .net Azure Search](https://www.nuget.org/packages/Microsoft.Azure.Search) e a propriedade do [analisador](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet) . 