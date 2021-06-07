---
title: Códigos de erro de dados - Azure Stream Analytics
description: Solucionar problemas do Azure Stream Analytics com códigos de erro de dados.
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 33e617c8c3589d76d649dd2ea2236a4247b12500
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98016483"
---
# <a name="azure-stream-analytics-data-error-codes"></a>Azure Stream Analytics códigos de erro de dados

Você pode usar logs de atividade e logs de recursos para ajudar a depurar comportamentos inesperados do seu trabalho do Azure Stream Analytics. Este artigo lista a descrição de cada código de erro de dados. Os erros de dados ocorrem quando há dados inválidos no fluxo, como um esquema de registro inesperado.

## <a name="inputdeserializationerror"></a>InputDeserializationError

* **Causa**: Ocorreu um erro ao desserializar os dados de entrada.

## <a name="inputeventtimestampnotfound"></a>InputEventTimestampNotFound

* **Causa**: O Stream Analytics não pode obter um carimbo de data/hora para o recurso. 

## <a name="inputeventtimestampbyovervaluenotfound"></a>InputEventTimestampByOverValueNotFound

* **Causa**: O Stream Analytics não pode obter o valor `TIMESTAMP BY OVER COLUMN`.

## <a name="inputeventlatebeyondthreshold"></a>InputEventLateBeyondThreshold

* **Causa**: Um evento de entrada foi enviado depois da tolerância configurada.

## <a name="inputeventearlybeyondthreshold"></a>InputEventEarlyBeyondThreshold

* **Causa**: Uma hora de chegada do evento de entrada é anterior ao limite do carimbo de data/hora do aplicativo do evento de entrada.

## <a name="azurefunctionmessagesizeexceeded"></a>AzureFunctionMessageSizeExceeded

* **Causa**: A saída da mensagem para Azure Functions excede o limite de tamanho.

## <a name="eventhuboutputrecordexceedssizelimit"></a>EventHubOutputRecordExceedsSizeLimit

* **Causa**: Um registro de saída excede o limite de tamanho máximo ao gravar no Hub de Eventos.

## <a name="cosmosdboutputinvalidid"></a>CosmosDBOutputInvalidId

* **Causa**: O valor ou o tipo de uma coluna específica é inválido.
* **Recomendação**: Forneça cadeias de caracteres não vazias e exclusivas que não tenham mais de 255 caracteres.

## <a name="cosmosdboutputinvalididcharacter"></a>CosmosDBOutputInvalidIdCharacter

* **Causa**: A ID do Documento do registro de saída contém um caractere inválido.

## <a name="cosmosdboutputmissingid"></a>CosmosDBOutputMissingId

* **Causa**: O registro de saída não contém a coluna \[id] a usar como a propriedade da chave primária.

## <a name="cosmosdboutputmissingidcolumn"></a>CosmosDBOutputMissingIdColumn

* **Causa**: O registro de saída não contém a propriedade da ID do Documento. 
* **Recomendação**: Verifique se a saída da consulta contém a coluna com uma cadeia de caracteres não vazia e exclusiva menor que “255” caracteres.

## <a name="cosmosdboutputmissingpartitionkey"></a>CosmosDBOutputMissingPartitionKey

* **Causa**: O registro de saída não tem a coluna a usar como a propriedade da chave de partição.

## <a name="cosmosdboutputsinglerecordtoolarge"></a>CosmosDBOutputSingleRecordTooLarge

* **Causa**: Uma gravação de registro único para o Cosmos DB é muito grande.

## <a name="sqldatabaseoutputdataerror"></a>SQLDatabaseOutputDataError

* **Causa**: O Stream Analytics não pode gravar evento(s) no Banco de Dados SQL devido a problemas nos dados.

## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas de conexões de entrada](stream-analytics-troubleshoot-input.md)
* [Solucionar problemas de saídas do Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Solucionar problemas de consultas do Azure Stream Analytics](stream-analytics-troubleshoot-query.md)
* [Solucionar problemas do Azure Stream Analytics usando logs de recursos](stream-analytics-job-diagnostic-logs.md)
* [Erros de dados do Azure Stream Analytics](data-errors.md)
