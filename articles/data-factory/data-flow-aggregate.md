---
title: Transformação Agregação no fluxo de dados de mapeamento
description: Saiba como agregar dados em escala em Azure Data Factory com a transformação agregar fluxo de dados de mapeamento.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/14/2020
ms.openlocfilehash: 71f5488b1f689e8892155b013730bcbb3c8e0e35
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90531909"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Transformação Agregação no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A transformação Agregação define as agregações de colunas em seus fluxos de dados. Usando o construtor de expressões, você pode definir diferentes tipos de agregações como SUM, MIN, MAX e COUNT agrupados por colunas computadas ou existentes.

## <a name="group-by"></a>Agrupar por

Selecione uma coluna existente ou crie uma nova coluna computada para usar como uma cláusula Group by para sua agregação. Para usar uma coluna existente, selecione-a na lista suspensa. Para criar uma nova coluna computada, passe o mouse sobre a cláusula e clique em **coluna computada**. Isso abre o [Construtor de expressões de fluxo de dados](concepts-data-flow-expression-builder.md). Depois de criar a coluna computada, insira o nome da coluna de saída sob o campo **nome como** . Se você quiser adicionar uma cláusula Group by adicional, passe o mouse sobre uma cláusula existente e clique no ícone de adição.

![Grupos de transformação agregados por configurações](media/data-flow/agg.png "Grupos de transformação agregados por configurações")

Uma cláusula Group by é opcional em uma transformação Agregação.

## <a name="aggregate-columns"></a>Colunas de agregação

Vá para a guia **agregações** para criar expressões de agregação. Você pode substituir uma coluna existente por uma agregação ou criar um novo campo com um novo nome. A expressão de agregação é inserida na caixa à direita ao lado do seletor de nome de coluna. Para editar a expressão, clique na caixa de texto e abra o construtor de expressões. Para adicionar mais colunas de agregação, clique em **Adicionar** acima da lista de colunas ou no ícone de adição ao lado de uma coluna de agregação existente. Escolha **Adicionar coluna** ou **Adicionar padrão de coluna**. Cada expressão de agregação deve conter pelo menos uma função de agregação.

![Configurações de agregação](media/data-flow/aggregate-columns.png "Configurações de agregação")

> [!NOTE]
> No modo de depuração, o construtor de expressões não pode produzir visualizações de dados com funções de agregação. Para exibir visualizações de dados para transformações agregadas, feche o construtor de expressões e exiba os dados por meio da guia ' Data preview '.

### <a name="column-patterns"></a>Padrões de coluna

Use [padrões de coluna](concepts-data-flow-column-pattern.md) para aplicar a mesma agregação a um conjunto de colunas. Isso é útil se você deseja persistir muitas colunas do esquema de entrada, pois elas são descartadas por padrão. Use uma heurística, como `first()` manter as colunas de entrada por meio da agregação.

## <a name="reconnect-rows-and-columns"></a>Reconectar linhas e colunas

As transformações agregadas são semelhantes às consultas SELECT de agregação do SQL. As colunas que não estão incluídas em sua cláusula Group by ou funções de agregação não fluirão para a saída da transformação Agregação. Se você quiser incluir outras colunas em sua saída agregada, execute um dos seguintes métodos:

* Use uma função de agregação como `last()` ou `first()` para incluir essa coluna adicional.
* Reingresse as colunas no fluxo de saída usando o [padrão de autojunção](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/).

## <a name="removing-duplicate-rows"></a>Removendo linhas duplicadas

Um uso comum da transformação agregação é remover ou identificar entradas duplicadas em dados de origem. Esse processo é conhecido como eliminação de duplicação. Com base em um conjunto de chaves Group by, use uma heurística de sua escolha para determinar qual linha duplicada manter. A heurística comum são `first()` , `last()` , `max()` e `min()` . Use [padrões de coluna](concepts-data-flow-column-pattern.md) para aplicar a regra a cada coluna, exceto para as colunas agrupar por.

![Eliminação de duplicação](media/data-flow/agg-dedupe.png "Eliminação de duplicação")

No exemplo acima, colunas `ProductID` e `Name` estão sendo usadas para Agrupamento. Se duas linhas tiverem os mesmos valores para essas duas colunas, elas serão consideradas duplicatas. Nessa transformação Agregação, os valores da primeira linha correspondidos serão mantidos e todos os outros serão removidos. Usando a sintaxe de padrão de coluna, todas as colunas cujos nomes não são `ProductID` e `Name` são mapeadas para o nome de coluna existente e recebem o valor da primeira linha correspondente. O esquema de saída é o mesmo que o esquema de entrada.

Para cenários de validação de dados, a `count()` função pode ser usada para contar quantas duplicatas existem.

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<incomingStream>
    aggregate(
           groupBy(
                <groupByColumnName> = <groupByExpression1>,
                <groupByExpression2>
               ),
           <aggregateColumn1> = <aggregateExpression1>,
           <aggregateColumn2> = <aggregateExpression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <aggregateTransformationName>
```

### <a name="example"></a>Exemplo

O exemplo abaixo usa um fluxo de entrada `MoviesYear` e agrupa linhas por coluna `year` . A transformação cria uma coluna de agregação `avgrating` que é avaliada como a média da coluna `Rating` . Essa transformação agregada é nomeada `AvgComedyRatingsByYear` .

No UX do Data Factory, essa transformação é semelhante à imagem abaixo:

![Agrupar por exemplo](media/data-flow/agg-script1.png "Agrupar por exemplo")

![Exemplo de agregação](media/data-flow/agg-script2.png "Exemplo de agregação")

O script de fluxo de dados para essa transformação está no trecho de código abaixo.

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

![Script de fluxo de dados de agregação](media/data-flow/aggdfs1.png "Script de fluxo de dados de agregação")

```MoviesYear```: Coluna derivada que define as colunas year e title ```AvgComedyRatingByYear``` : transformação agregação para classificação média de Comedies agrupadas por ano ```avgrating``` : nome da nova coluna que está sendo criada para conter o valor agregado

```
MoviesYear aggregate(groupBy(year),
    avgrating = avg(toInteger(Rating))) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>Próximas etapas

* Definir a agregação baseada em janela usando a [transformação janela](data-flow-window.md)
