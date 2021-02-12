---
title: Transformação de classificação no fluxo de dados de mapeamento
description: Como usar a transformação classificação de fluxo de dados de mapeamento do Azure Data Factory gerar uma coluna de classificação
author: dcstwh
ms.author: weetok
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/05/2020
ms.openlocfilehash: b7adb6bf13cba5f886b442515e8ba5661cfeb8ef
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490917"
---
# <a name="rank-transformation-in-mapping-data-flow"></a>Transformação de classificação no fluxo de dados de mapeamento 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use a transformação classificação para gerar uma classificação ordenada com base nas condições de classificação especificadas pelo usuário. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GGJo]

## <a name="configuration"></a>Configuração

![Configurações de classificação](media/data-flow/rank-configuration.png "Configurações de classificação")

Não **diferencia maiúsculas de minúsculas:** Se uma coluna de classificação for do tipo cadeia de caracteres, o caso será fatorado na classificação. 

**Denso:** Se habilitada, a coluna de classificação terá uma classificação densa. Cada contagem de classificação será um número consecutivo e os valores de classificação não serão ignorados após um empate.

**Coluna de classificação:** O nome da coluna de classificação gerada. Esta coluna será do tipo Long.

**Condições de classificação:** Escolha quais colunas você está classificando e em qual ordem a classificação ocorre. A ordem determina a prioridade de classificação.

A configuração acima usa dados de basquete de entrada e cria uma coluna de classificação chamada ' pointsRanking '. A linha com o valor mais alto da coluna *pts* terá um valor de *pointsRanking* de 1.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<incomingStream>
    rank(
        desc(<sortColumn1>),
        asc(<sortColumn2>),
        ...,
        caseInsensitive: { true | false }
        dense: { true | false }
        output(<rankColumn> as long)
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>Exemplo

![Configurações de classificação](media/data-flow/rank-configuration.png "Configurações de classificação")

O script de fluxo de dados para a configuração de classificação acima está no trecho de código a seguir.

```
PruneColumns
    rank(
        desc(PTS, true),
        caseInsensitive: false,
        output(pointsRanking as long),
        dense: false
    ) ~> RankByPoints
```

## <a name="next-steps"></a>Próximas etapas

Filtrar linhas com base nos valores de classificação usando a [transformação filtro](data-flow-filter.md).
