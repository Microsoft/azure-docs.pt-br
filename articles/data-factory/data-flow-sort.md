---
title: Transformação classificação no fluxo de dados de mapeamento
description: Transformação de classificação de dados de mapeamento Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 26852ec77194714c8236856b7cb496170bf0d777
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81606332"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>Transformação classificação no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A transformação classificação permite que você classifique as linhas de entrada no fluxo de dados atual. Você pode escolher colunas individuais e classificá-las em ordem crescente ou decrescente.

> [!NOTE]
> O mapeamento de fluxos de dados é executado em clusters do Spark que distribuem dados entre vários nós e partições. Se você optar por reparticionar seus dados em uma transformação subsequente, poderá perder sua classificação devido a embaralhando de dados.

## <a name="configuration"></a>Configuração

![Configurações de classificação](media/data-flow/sort.png "Sort")

Não **diferencia maiúsculas de minúsculas:** Se você deseja ou não ignorar maiúsculas e minúsculas ao classificar campos de cadeia de caracteres ou texto

**Classificar somente dentro das partições:** À medida que os fluxos de dados são executados no Spark, cada fluxo de dados é dividido em partições. Essa configuração classifica os dados somente dentro das partições de entrada em vez de classificar todo o fluxo de dados. 

**Condições de classificação:** Escolha quais colunas você está classificando e em qual ordem a classificação ocorre. A ordem determina a prioridade de classificação. Escolha se os nulos aparecerão ou não no início ou no final do fluxo de dados.

### <a name="computed-columns"></a>Colunas computadas

Para modificar ou extrair um valor de coluna antes de aplicar a classificação, passe o mouse sobre a coluna e selecione "coluna computada". Isso abrirá o construtor de expressões para criar uma expressão para a operação de classificação em vez de usar um valor de coluna.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<incomingStream>
    sort(
        desc(<sortColumn1>, { true | false }),
        asc(<sortColumn2>, { true | false }),
        ...
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>Exemplo

![Configurações de classificação](media/data-flow/sort.png "Sort")

O script de fluxo de dados para a configuração de classificação acima está no trecho de código abaixo.

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>Próximas etapas

Após a classificação, talvez você queira usar a [transformação Agregação](data-flow-aggregate.md)
