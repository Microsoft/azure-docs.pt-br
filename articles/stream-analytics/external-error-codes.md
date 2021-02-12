---
title: Códigos de erros externos - Azure Stream Analytics
description: Solucionar problemas do Azure Stream Analytics com códigos de erros externos.
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 9f55a715b11b126ea340e665e008d7245e578190
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98016381"
---
# <a name="azure-stream-analytics-external-error-codes"></a>Códigos de erros externos do Azure Stream Analytics

Você pode usar logs de atividade e logs de recursos para ajudar a depurar comportamentos inesperados do seu trabalho do Azure Stream Analytics. Este artigo lista a descrição de cada código de erro externo. Erros externos são erros genéricos lançados por um serviço upstream ou downstream que o Stream Analytics não consegue diferenciar como erro de dados, erro de configuração ou erro de disponibilidade externa.

## <a name="adapterinitializationerror"></a>AdapterInitializationError

* **Causa**: Erro ao inicializar um adaptador.

## <a name="adapterfailedtowriteevents"></a>AdapterFailedToWriteEvents

* **Causa**: Erro ao gravar dados em um adaptador.

## <a name="azurefunctionhttperror"></a>AzureFunctionHttpError

* **Causa**: Um erro de HTTP foi retornado pelo Azure Functions.

## <a name="azurefunctionfailedtosendmessage"></a>AzureFunctionFailedToSendMessage

* **Causa**: Falha do Stream Analytics ao gravar eventos no Azure Functions.

## <a name="azurefunctionredirecterror"></a>AzureFunctionRedirectError

* **Causa**: Erro de redirecionamento durante a saída para Azure Functions.

## <a name="azurefunctionclienterror"></a>AzureFunctionClientError

* **Causa**: Erro do cliente na saída para Azure Functions.

## <a name="azurefunctionservererror"></a>AzureFunctionServerError

* **Causa**: Erro do servidor na saída para Azure Functions.

## <a name="azurefunctionhttptimeouterror"></a>AzureFunctionHttpTimeOutError

* **Causa**: Falha ao gravar no Azure Functions, pois a solicitação HTTP excedeu o tempo limite. 
* **Recomendação**: Verifique os logs do Azure Functions em busca de possíveis atrasos.

## <a name="eventhubargumenterror"></a>EventHubArgumentError

* **Causa**: Os deslocamentos de entrada são inválidos. Isso pode ocorrer devido a um failover.
* **Recomendação**: Reinicie o trabalho do Stream Analytics pela hora da última saída.

## <a name="eventhubfailedtowriteevents"></a>EventHubFailedToWriteEvents

* **Causa**: Erro ao enviar dado para o hub de eventos.

## <a name="cosmosdbconnectionfailureaftermaxretries"></a>CosmosDBConnectionFailureAfterMaxRetries

* **Causa**: Falha do Stream Analytics ao se conectar a uma conta do Cosmos DB após o número máximo de tentativas.

## <a name="cosmosdbfailureaftermaxretries"></a>CosmosDBFailureAfterMaxRetries

* **Causa**: Falha do Stream Analytics ao consultar o banco de dados e a coleção do Cosmos DB após o número máximo de tentativas.

## <a name="cosmosdbfailedtocreatestoredprocedure"></a>CosmosDBFailedToCreateStoredProcedure

* **Causa**: O Cosmos DB não pode criar um procedimento armazenado após várias tentativas.

## <a name="cosmosdboutputrequesttimeout"></a>CosmosDBOutputRequestTimeout

* **Causa**: O procedimento armazenado upsert retornou um erro. 

## <a name="sqldatabaseoutputinitializationerror"></a>SQLDatabaseOutputInitializationError

* **Causa**: O Stream Analytics não pode inicializar a saída do Banco de Dados SQL.

## <a name="sqldatabaseoutputwriteerror"></a>SQLDatabaseOutputWriteError

* **Causa**: O Stream Analytics não pode gravar eventos na saída do Banco de Dados SQL.

## <a name="sqldwoutputinitializationerror"></a>SQLDWOutputInitializationError

* **Causa**: ocorreu um erro ao inicializar uma saída de pool de SQL dedicada.

## <a name="sqldwoutputwriteerror"></a>SQLDWOutputWriteError

* **Causa**: ocorreu um erro ao gravar a saída em um pool SQL dedicado.

## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas de conexões de entrada](stream-analytics-troubleshoot-input.md)
* [Solucionar problemas de saídas do Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Solucionar problemas de consultas do Azure Stream Analytics](stream-analytics-troubleshoot-query.md)
* [Solucionar problemas do Azure Stream Analytics usando logs de recursos](stream-analytics-job-diagnostic-logs.md)
* [Erros de dados do Azure Stream Analytics](data-errors.md)
