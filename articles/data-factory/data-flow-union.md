---
title: Transformação de Ramificação do Novo Fluxo de Dados de Mapeamento do Azure Data Factory
description: Transformação de Ramificação do Novo Fluxo de Dados de Mapeamento do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: af2225d749283c7124f89d5a7cd735b2f6bfd121
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61347952"
---
# <a name="mapping-data-flow-union-transformation"></a>Mapeamento de transformação union de fluxo de dados

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A União combinará vários fluxos de dados em um, com o SQL Union desses fluxos como a nova saída da transformação de União. Todas as do esquema de cada fluxo de entrada serão combinadas dentro de seu fluxo de dados, sem a necessidade de ter uma chave de junção.

Você pode combinar n-número de fluxos na tabela de configurações, selecionando o ícone "+" ao lado de cada linha configurado, incluindo dados de origem, bem como fluxos de transformações existentes em seu fluxo de dados.

![A transformação Union](media/data-flow/union.png "Union")

Nesse caso, você pode combinar diferentes metadados de várias fontes (no exemplo, três arquivos de origem diferente) e combiná-los em um único fluxo:

![Visão geral da transformação Union](media/data-flow/union111.png "1 Union")

Para fazer isso, adicione linhas adicionais nas configurações de união, incluindo todos os fonte que você deseja adicionar. Não é necessário para uma chave de pesquisa ou de junção comum:

![Configurações de transformação Union](media/data-flow/unionsettings.png "configurações Union")

Se você definir uma transformação Select após a união, você poderá renomear sobrepostos campos ou campos que não foram nomeados de fontes sem cabeçalho. Clique em "Inspect" para ver os metadados de combinar com 132 colunas total neste exemplo de três fontes diferentes:

![Final de transformação Union](media/data-flow/union333.png "3 Union")

## <a name="name-and-position"></a>Nome e a posição

Quando você escolhe "pelo nome de união", cada valor de coluna cairá na coluna correspondente em cada origem, com um novo esquema de metadados relacionados.

Se você escolher "união por posição", cada valor de coluna descartará na posição original em cada origem correspondente, resultando em um novo fluxo combinado onde os dados em cada origem são adicionados ao mesmo fluxo de dados:

![Saída de união](media/data-flow/unionoutput.png "saída Union")

## <a name="next-steps"></a>Próximas etapas

Explore as transformações semelhantes incluindo [ingressar](data-flow-join.md) e [Exists](data-flow-exists.md).
