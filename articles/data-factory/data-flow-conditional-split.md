---
title: Transformação de divisão condicional no fluxo de dados de mapeamento
description: Dividir dados em fluxos diferentes usando a transformação de divisão condicional no fluxo de dados de mapeamento do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/21/2020
ms.openlocfilehash: eece6f97e82f3800d4f59ac1849b34c2a1e4635b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "83800077"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>Transformação de divisão condicional no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A transformação de divisão condicional roteia as linhas de dados para diferentes fluxos com base em condições de correspondência. A transformação de divisão condicional é semelhante a uma estrutura de decisão CASE em uma linguagem de programação. A transformação avalia expressões, e com base nos resultados, direciona a linha de dados para o fluxo especificado.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4wKCX]

## <a name="configuration"></a>Configuração

A configuração **Dividir** determina se a linha de dados flui para o primeiro fluxo de correspondência ou para cada fluxo ao qual ela corresponde.

Use o construtor de expressões de fluxo de dados para inserir uma expressão para a condição de divisão. Para adicionar uma nova condição, clique no ícone de adição em uma linha existente. Um fluxo padrão também pode ser adicionado para linhas que não correspondem a nenhuma condição.

![Divisão condicional](media/data-flow/conditionalsplit1.png "opções de divisão condicional")

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<incomingStream>
    split(
        <conditionalExpression1>
        <conditionalExpression2>
        ...
        disjoint: {true | false}
    ) ~> <splitTx>@(stream1, stream2, ..., <defaultStream>)
```

### <a name="example"></a>Exemplo

O exemplo abaixo é uma transformação de divisão condicional chamada `SplitByYear` que usa o fluxo de entrada `CleanData`. Essa transformação tem duas condições de divisão: `year < 1960` e `year > 1980`. `disjoint` é False porque os dados vão para a primeira condição de correspondência. Cada linha que corresponde à primeira condição vai para o fluxo de saída `moviesBefore1960`. Todas as linhas restantes que correspondem à segunda condição vão para o fluxo de saída `moviesAFter1980`. Todas as outras linhas fluem por meio do fluxo padrão `AllOtherMovies`.

No UX do Data Factory, essa transformação é semelhante à imagem abaixo:

![Divisão condicional](media/data-flow/conditionalsplit1.png "opções de divisão condicional")

O script de fluxo de dados para essa transformação está no trecho de código abaixo:

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>Próximas etapas

As transformações de fluxo de dados comuns usadas com divisão condicional são as [transformação de junção](data-flow-join.md), a [transformação de pesquisa](data-flow-lookup.md) e a [transformação de seleção](data-flow-select.md)
