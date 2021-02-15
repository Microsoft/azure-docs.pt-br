---
title: Transformação de filtro no fluxo de dados de mapeamento
description: Filtrar linhas usando a transformação de filtro em Azure Data Factory fluxo de dados de mapeamento
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/26/2020
ms.openlocfilehash: 8189228d6707812fb943e9925dc2bbf1b6da4972
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84112806"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Transformação de filtro no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

As transformações de filtro permitem a filtragem de linha com base em uma condição. O fluxo de saída inclui todas as linhas que correspondem à condição de filtragem. A transformação de filtro é semelhante a uma cláusula WHERE no SQL.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xnxN]

## <a name="configuration"></a>Configuração

Use o construtor de expressões de fluxo de dados para inserir uma expressão para a condição de filtro. Para abrir o construtor de expressões, clique na caixa azul. A condição de filtro deve ser do tipo booliano. Para obter mais informações sobre como criar uma expressão, consulte a documentação do [Construtor de expressões](concepts-data-flow-expression-builder.md) .

![Transformação de filtro](media/data-flow/filter1.png "Transformação de filtro")

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>Exemplo

O exemplo abaixo é uma transformação de filtro chamada `FilterBefore1960` que usa o fluxo de entrada `CleanData` . A condição de filtro é a expressão `year <= 1960` .

No UX do Data Factory, essa transformação é semelhante à imagem abaixo:

![Transformação de filtro](media/data-flow/filter1.png "Transformação de filtro")

O script de fluxo de dados para essa transformação está no trecho de código abaixo:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Próximas etapas

Filtrar colunas com a [transformação selecionar](data-flow-select.md)
