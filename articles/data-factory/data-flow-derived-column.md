---
title: Transformação de coluna derivada no fluxo de dados de mapeamento
description: Saiba como transformar dados em escala no Azure Data Factory com a transformação de coluna derivada do fluxo de dados de mapeamento.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/14/2020
ms.openlocfilehash: 2e90a8779322cf8967ca9a194c6cc760f7c8b8f5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90531935"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Transformação de coluna derivada no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use a transformação de coluna derivada para gerar novas colunas no fluxo de dados ou modificar os campos existentes.

## <a name="create-and-update-columns"></a>Criar e atualizar colunas

Ao criar uma coluna derivada, você pode gerar uma nova coluna ou atualizar uma existente. Na caixa de texto **coluna** , insira na coluna que você está criando. Para substituir uma coluna existente em seu esquema, você pode usar a lista suspensa coluna. Para criar a expressão da coluna derivada, clique na caixa de texto **Inserir expressão** . Você pode começar a digitar sua expressão ou abrir o construtor de expressões para construir sua lógica.

![Configurações de coluna derivada](media/data-flow/create-derive-column.png "Configurações de coluna derivada")

Para adicionar mais colunas derivadas, clique em **Adicionar** acima da lista de colunas ou no ícone de adição ao lado de uma coluna derivada existente. Escolha **Adicionar coluna** ou **Adicionar padrão de coluna**.

![Seleção de nova coluna derivada](media/data-flow/add-derived-column.png "Seleção de nova coluna derivada")

### <a name="column-patterns"></a>Padrões de coluna

Nos casos em que o esquema não está definido explicitamente ou se você deseja atualizar um conjunto de colunas em massa, você desejará criar a coluna padrões. Os padrões de coluna permitem que você corresponda colunas usando regras com base nos metadados da coluna e crie colunas derivadas para cada coluna correspondente. Para obter mais informações, saiba [como criar padrões de coluna](concepts-data-flow-column-pattern.md#column-patterns-in-derived-column-and-aggregate) na transformação coluna derivada.

![Padrões de coluna](media/data-flow/column-pattern-derive.png "Padrões de coluna")

## <a name="building-schemas-using-the-expression-builder"></a>Criando esquemas usando o construtor de expressões

Ao usar o [Construtor de expressões](concepts-data-flow-expression-builder.md)de fluxo de dados de mapeamento, você pode criar, editar e gerenciar suas colunas derivadas na seção **colunas derivadas** . Todas as colunas que são criadas ou alteradas na transformação são listadas. Escolha interativamente qual coluna ou padrão você está editando clicando no nome da coluna. Para adicionar uma coluna adicional, selecione **criar nova** e escolha se deseja adicionar uma única coluna ou um padrão.

![Criar nova coluna](media/data-flow/derive-add-column.png "Criar nova coluna")

Ao trabalhar com colunas complexas, você pode criar Subcolunas. Para fazer isso, clique no ícone de adição ao lado de qualquer coluna e selecione **Adicionar subcoluna**. Para obter mais informações sobre como processar tipos complexos no fluxo de dados, confira [Tratamento de JSON no fluxo de dados de mapeamento](format-json.md#mapping-data-flow-properties).

![Adicionar subcoluna](media/data-flow/derive-add-subcolumn.png "Adicionar subcoluna")

Para obter mais informações sobre como processar tipos complexos no fluxo de dados, confira [Tratamento de JSON no fluxo de dados de mapeamento](format-json.md#mapping-data-flow-properties).

![Adicionar coluna complexa](media/data-flow/derive-complex-column.png "Adicionar colunas")

### <a name="locals"></a>Locais

Se você estiver compartilhando a lógica em várias colunas ou quiser criar uma compartimentalização da lógica, crie um local dentro de uma transformação coluna derivada. Um local é um conjunto de lógica que não é propagada downstream para a transformação a seguir. Os locais podem ser criados no construtor de expressões acessando **elementos de expressão** e selecionando **locais**. Crie um novo selecionando **criar novo**.

![Criar local](media/data-flow/create-local.png "Criar local")

Os locais podem referenciar qualquer elemento de expressão como uma coluna derivada, incluindo funções, esquema de entrada, parâmetros e outros locais. Ao fazer referência a outros locais, o pedido é importante, pois o local referenciado precisa ser "acima" do atual.

![Criar local 2](media/data-flow/create-local-2.png "Criar local 2")

Para fazer referência a um local em uma coluna derivada, clique no local na exibição de **elementos de expressão** ou faça referência a ele com dois-pontos na frente de seu nome. Por exemplo, um local chamado local1 seria referenciado por `:local1` . Para editar uma definição local, passe o mouse sobre ela na exibição elementos de expressão e clique no ícone de lápis.

![Usando locais](media/data-flow/using-locals.png "Usando locais")

## <a name="data-flow-script"></a>Script de fluxo de dados

### <a name="syntax"></a>Sintaxe

```
<incomingStream>
    derive(
           <columnName1> = <expression1>,
           <columnName2> = <expression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <deriveTransformationName>
```

### <a name="example"></a>Exemplo

O exemplo abaixo é uma coluna derivada chamada `CleanData` que usa um fluxo de entrada `MoviesYear` e cria duas colunas derivadas. A primeira coluna derivada substitui a coluna `Rating` pelo valor da classificação como um tipo inteiro. A segunda coluna derivada é um padrão que faz a correspondência de cada coluna cujo nome começa com 'movies'. Para cada coluna correspondente, ele cria uma coluna `movie` que é igual ao valor da coluna correspondente prefixada com 'movie_'. 

No UX do Data Factory, essa transformação é semelhante à imagem abaixo:

![Obter um exemplo](media/data-flow/derive-script.png "Obter um exemplo")

O script de fluxo de dados para essa transformação está no trecho de código abaixo:

```
MoviesYear derive(
                Rating = toInteger(Rating),
                each(
                    match(startsWith(name,'movies')),
                    'movie' = 'movie_' + toString($$)
                )
            ) ~> CleanData
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [linguagem de expressão do fluxo de dados de mapeamento](data-flow-expression-functions.md).
