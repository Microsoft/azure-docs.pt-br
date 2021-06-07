---
title: Evento de dimensionamento automático do pool do lote do Azure
description: Referência para o evento de dimensionamento automático do pool do lote, que é emitido quando um dimensionamento automático do pool é executado. O conteúdo do log irá expor a fórmula de autoescala e os resultados de avaliação para o pool.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: e548ed484e5f683a8f79434ce4095ac66900f01a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91852089"
---
# <a name="pool-autoscale-event"></a>Evento de dimensionamento automático de pool

 Esse evento é emitido quando um dimensionamento automático de pool é executado. O conteúdo do log irá expor a fórmula de autoescala e os resultados de avaliação para o pool.

 O exemplo a seguir mostra o corpo de um evento de dimensionamento automático do pool para um dimensionamento automático do pool, que falhou devido a dados de exemplo insuficientes.

```
{
    "id": "myPool1",
    "timestamp": "2020-09-21T18:59:56.204Z",
    "formula": "...",
    "results": "...",
    "error": {
        "code": "InsufficientSampleData",
        "message": "Autoscale evaluation failed due to insufficient sample data",
        "values": [{
                "name": "Message",
                "value": "Line 15, Col 44: Insufficient data from data set: $RunningTasks wanted 70%, received 50%"
            }
        ]
    }
}
```

|Elemento|Type|Observações|
|-------------|----------|-----------|
|`id`|String|A ID do pool.|
|`timestamp`|Datetime|O carimbo de data/hora quando o dimensionamento automático é executado.|
|`formula`|String|A fórmula definida para o dimensionamento automático.|
|`results`|String|Resultados da avaliação para todas as variáveis usadas na fórmula.|
|[`error`](#error)|Tipo complexo|O erro detalhado para o dimensionamento automático.|

###  <a name="error"></a><a name="error"></a> erro(s)

|Nome do elemento|Type|Observações|
|------------------|----------|-----------|
|`code`|String|Um identificador para o erro de dimensionamento automático. Os códigos são invariáveis e devem ser consumidos programaticamente.|
|`message`|String|Uma mensagem que descreve o erro de dimensionamento automático, destinada a ser adequada para exibição em uma interface do usuário.|
|`values`|Array|Lista de pares nome-valor que descrevem mais detalhes do erro de dimensionamento automático.|
