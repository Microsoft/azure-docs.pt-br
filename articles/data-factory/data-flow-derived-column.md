---
title: Derivado de transformação de coluna no mapeamento de fluxo de dados - Azure Data Factory | Microsoft Docs
description: Saiba como transformar dados em escala no Azure Data Factory com a mapeamento de dados de fluxo transformação coluna derivada.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 941c629fd8359edc7fc1cf364a6735314044d95e
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312176"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Transformação coluna derivada no mapeamento de fluxo de dados

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Use a transformação de Coluna Derivada para gerar novas colunas em seu fluxo de dados ou modificar campos existentes.

## <a name="derived-column-settings"></a>Configurações de coluna derivadas

Para substituir uma coluna existente, selecione-o por meio do menu suspenso de coluna. Caso contrário, use o campo de seleção de coluna como uma caixa de texto e digite o novo nome da coluna. Para compilar a expressão da coluna derivada, clique na caixa 'Inserir a expressão' para abrir o [construtor de expressões de fluxo de dados](concepts-data-flow-expression-builder.md).

![Derivado de configurações de coluna](media/data-flow/dc1.png "derivadas as configurações de coluna")

Para adicionar mais colunas derivadas, passe o mouse sobre um existente derivada de coluna e clique em '+'. Em seguida, escolha 'Adicionar coluna' ou 'Adicionar coluna padrão'. Padrões de coluna podem ser útil se os nomes de coluna são variáveis de suas fontes. Para obter mais informações, consulte [padrões de coluna](concepts-data-flow-column-pattern.md).

![Novo derivado a seleção de coluna](media/data-flow/columnpattern.png "New derivado a seleção de coluna")

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [linguagem de expressão de mapeamento de fluxo de dados](data-flow-expression-functions.md).
