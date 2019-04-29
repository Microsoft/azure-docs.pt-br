---
title: Transformação não dinâmica do Fluxo de Dados de Mapeamento do Azure Data Factory
description: Transformação não dinâmica do Fluxo de Dados de Mapeamento do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 160ff8bbcb8ce5a4f1f32245cc366281640c5919
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61347988"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Transformação não dinâmica do Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Use Transformar colunas em linhas no Fluxo de Dados de Mapeamento do ADF como uma maneira de transformar um conjunto de dados não normalizado em uma versão mais normalizada por meio da expansão de valores de colunas múltiplas em um único registro em vários registros com os mesmos valores em uma coluna única.

![Transformação não dinâmica](media/data-flow/unpivot1.png "Opções não dinâmicas 1")

## <a name="ungroup-by"></a>Desagrupar por

![Transformação não dinâmica](media/data-flow/unpivot5.png "Opções não dinâmicas 2")

Primeiro, defina as colunas pelas quais você deseja agrupar para sua agregação de tabela dinâmica. Defina uma ou mais colunas para desagrupamento com o sinal + ao lado da lista de colunas.

## <a name="unpivot-key"></a>Chave não dinâmica

![Transformação não dinâmica](media/data-flow/unpivot6.png "Opções não dinâmicas 3")

A chave de dinamização é a coluna que o ADF dinamizará de linha para coluna. Por padrão, cada valor único no conjunto de dados para o campo será dinamizado para uma coluna. No entanto, é possível inserir, opcionalmente, os valores do conjunto de dados que você deseja dinamizar para valores de coluna.

## <a name="unpivoted-columns"></a>Colunas não dinâmicas

![Transformação não dinâmica](media/data-flow//unpivot7.png "Opções não dinâmicas 4")

Por fim, escolha a agregação que você deseja usar para os valores dinâmicos e como você gostaria que as colunas fossem exibidas na nova projeção de saída da transformação.

(Opcional) É possível definir um padrão de nomenclatura com um prefixo, um intermediário e um sufixo a serem adicionados a cada novo nome de coluna com base nos valores de linha.

Por exemplo, dinamizar “Vendas” por “Região” simplesmente forneceria novos valores de coluna de cada valor de vendas. Por exemplo:  "25", "50", "1000", ... No entanto, se você definir um valor de prefixo de “Vendas”, então “Vendas” será prefixado para os valores.

<img src="media/data-flow/unpivot3.png" width="400">

Definir a Disposição de Colunas como “Normal” agrupará todas as colunas dinâmicas com seus valores agregados. Definir a disposição de colunas como “Lateral” alternará entre coluna e valor.

![Transformação não dinâmica](media/data-flow//unpivot7.png "Opções não dinâmicas 5")

O conjunto final de resultados de dados não dinâmicos mostra os totais de colunas agora transformadas em valores de linha separados.

## <a name="next-steps"></a>Próximas etapas

Use o [transformação dinâmica](data-flow-pivot.md) para pivot linhas para as colunas.
