---
title: Padrões de Coluna do Fluxo de Dados do Mapeamento do Azure Data Factory
description: Criar padrões de transformação de dados generalizados usando Azure Data Factory padrões de coluna no mapeamento de fluxos de dados
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 076c3318a68a50e6bd1b4f9f2a4a4b9a034533c6
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68346578"
---
# <a name="azure-data-factory-mapping-data-flows-column-patterns"></a>Padrões de coluna de fluxos de dados de mapeamento de data factory do Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Várias transformações de Fluxo de Dados do Azure Data Factory dão suporte à ideia de "Padrões de colunas" para que você possa criar colunas de modelo com base nos padrões em vez de nomes de coluna embutidos em código. Você pode usar esse recurso no construtor de expressões para definir padrões para corresponder colunas para transformação em vez de exigir nomes de campo específicos e exatos. Padrões são úteis se os campos de origem de entrada são alterados com frequência, especialmente no caso de alteração de colunas em arquivos de texto ou bancos de dados NoSQL. Essa condição às vezes é chamada de "descompasso de esquema".

![padrões de coluna](media/data-flow/columnpattern2.png "Padrões de coluna")

Padrões de coluna são úteis para lidar com cenários de Descompasso de esquema, bem como cenários gerais. É uma boa opção para condições em que não é possível saber totalmente cada nome de coluna. Você pode padronizar a correspondência no nome de coluna e no tipo de dados de coluna e criar uma expressão para a transformação que executará essa operação em relação a qualquer campo no fluxo de dados que corresponde aos seus padrões `name` & `type`.

Ao adicionar uma expressão a uma transformação que aceita padrões, escolha "Adicionar coluna padrão". Padrões de coluna permitem que a coluna de descompasso do esquema corresponda aos padrões.

Ao criar padrões de coluna de modelo, use `$$` na expressão para declarar uma referência para cada campo correspondente do fluxo de dados de entrada.

Se optar por usar uma das funções regex do Construtor de Expressões, você poderá, posteriormente, usar $1, $2, $3 ... para fazer referência a subpadrões com correspondência de sua expressão regex.

Um exemplo de cenário de Padrão de Coluna está usando SUM com uma série de campos de entrada. Os cálculos SUM agregados ficam na transformação de agregação. Em seguida, você pode usar SUM em cada correspondência de tipos de campo que correspondem a "Integer" e, em seguida, use $ $ para fazer referência a cada correspondência em sua expressão.

## <a name="match-columns"></a>Colunas de correspondência
![tipos de padrões de coluna](media/data-flow/pattern2.png "Tipos de padrões")

Para criar padrões com base em colunas, você pode corresponder ao nome da coluna, ao tipo, ao fluxo ou à posição e usar qualquer combinação delas com funções de expressão e expressões regulares.

![posição da coluna](media/data-flow/position.png "Posição da coluna")

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a [linguagem de expressão](https://aka.ms/dataflowexpressions) de fluxo de dados de mapeamento do ADF para transformações de dados
