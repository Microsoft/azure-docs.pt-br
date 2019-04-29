---
title: Transformação de Chave Alternativa do Fluxo de Dados de Mapeamento do Azure Data Factory
description: Como usar de mapeamento de fluxo alternativo chave transformação da Azure Data Factory de dados para gerar valores de chave sequenciais
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: eaa1c577f7e208400d3430222b006e0dbbd7956a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61350279"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Mapeamento de transformação chave de substitutos de fluxo de dados

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Use a Transformação de Chave Alternativa para adicionar um valor de chave crescente, arbitrário e não comercial ao conjunto de linhas de seu fluxo de dados. Isso é útil durante a criação de tabelas de dimensões em um modelo de dados analítico de esquema em estrela em que cada membro das tabelas precise ter uma chave exclusiva que seja não comercial, parte importante da metodologia Kimball de DWs.

![Transformar Chave Alternativa](media/data-flow/surrogate.png "Transformação de Chave Alternativa")

"Coluna de Chave" é o nome que você dará à sua nova coluna de chave alternativa.

"Valor Inicial" é o ponto de partida do valor incremental.

## <a name="increment-keys-from-existing-sources"></a>Chaves de incremento de fontes existentes

Se você quiser iniciar sua sequência de um valor que existe em uma fonte, você pode usar uma transformação coluna derivada imediatamente após sua transformação de chave substituta e somar dois valores:

![SK adicionar máx](media/data-flow/sk006.png "substituto chave transformação adicionar máx")

Para propagar o valor da chave com o máximo anterior, há duas técnicas que você pode usar:

### <a name="database-sources"></a>Fontes de banco de dados

Use a opção de "Consulta" Selecionar Max () do seu código-fonte usando a transformação de origem:

![Substitutos de consulta principais](media/data-flow/sk002.png "substitutos de consulta de transformação de chave")

### <a name="file-sources"></a>Fontes de arquivo

Se o valor máximo anterior está em um arquivo, você pode usar a transformação de código-fonte juntamente com uma transformação agregação e usar a função de expressão Max () para obter o valor máximo anterior:

![Substitutos de arquivo de chave](media/data-flow/sk008.png "substitutos de arquivo de chave")

Em ambos os casos, você deve unir seus novos dados a entrada junto com seu código-fonte que contém o valor máximo anterior:

![Junção de chave de substitutos](media/data-flow/sk004.png "substitutos de junção de chave")

## <a name="next-steps"></a>Próximas etapas

Esses exemplos usam o [ingressar](data-flow-join.md) e [coluna derivada](data-flow-derived-column.md) transformações.
