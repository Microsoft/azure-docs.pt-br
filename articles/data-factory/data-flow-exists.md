---
title: Transformação de Ocorrências no Fluxo de Dados de Mapeamento do Azure Data Factory
description: Transformação de Ocorrências no Fluxo de Dados de Mapeamento do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 6ce27ba699ae766ed4d2428f67d91379464bb9f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730967"
---
# <a name="azure-data-factory-mapping-data-flow-exists-transformation"></a>Transformação de Ocorrências no Fluxo de Dados de Mapeamento do Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A transformação de ocorrências é uma transformação de filtragem de linhas que impede ou permite que as linhas de dados façam a passagem. A Transformação de Ocorrências é semelhante a ```SQL WHERE EXISTS``` e ```SQL WHERE NOT EXISTS```. Depois de uma transformação de filtro, as linhas resultantes do seu fluxo de dados incluirão todas as linhas onde os valores de coluna da fonte 1 existirem ou não na fonte 2.

![Configurações de ocorrências](media/data-flow/exsits.png "ocorrências 1")

Escolha a segunda fonte para suas ocorrências de modo que o fluxo de dados possa comparar os valores do fluxo 1 com os do fluxo 2.

Selecione as colunas da fonte 1 e da fonte 2 cujos valores quiser verificar se existem ou não.

## <a name="multiple-exists-conditions"></a>Várias condições de existe

Ao lado de cada linha em suas condições de coluna para há, você encontrará um + sinal disponíveis quando você focaliza alcançar linha. Isso permitirá que você adicione várias linhas para condições de Exists.

## <a name="next-steps"></a>Próximas etapas

