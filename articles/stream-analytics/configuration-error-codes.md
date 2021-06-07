---
title: Códigos de erro de configuração - Azure Stream Analytics
description: Solucionar problemas do Azure Stream Analytics com códigos de erro de configuração.
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 6137ceeb0d86b1531adab910175ddbc4722ef858
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019509"
---
# <a name="azure-stream-analytics-configuration-error-codes"></a>Códigos de erro de configuração do Azure Stream Analytics

Você pode usar logs de atividade e logs de recursos para ajudar a depurar comportamentos inesperados do seu trabalho do Azure Stream Analytics. Este artigo lista a descrição de cada código de erro de configuração. Os erros de configuração estão relacionados à configuração do trabalho ou às configurações de entrada e saída.

## <a name="eventhubunauthorizedaccess"></a>EventHubUnauthorizedAccess

* **Causa**: O hub de eventos gerou um erro de *acesso não autorizado*.

## <a name="eventhubreceiverepochconflict"></a>EventHubReceiverEpochConflict

* **Causa**: Há mais de um receptor do hub de eventos com valores de época diferentes.
* **Recomendação**: Verifique se o *Service Bus Explorer* ou um aplicativo *EventProcessorHost* não está conectado durante a execução do trabalho do Stream Analytics.

## <a name="eventhubreceiverquotaexceeded"></a>EventHubReceiverQuotaExceeded

* **Causa**: O Stream Analytics não pode se conectar a uma partição porque o número máximo de receptores permitidos por partição em um grupo de consumidores foi alcançado.
* **Recomendação**: Verifique se outros trabalhos do Stream Analytics ou do Service Bus Explorer não estão usando o mesmo grupo de consumidores.

## <a name="eventhuboutputthrottled"></a>EventHubOutputThrottled

* **Causa**: Erro ao gravar dados no hub de eventos devido à limitação.
* **Recomendação**: Se isso ocorrer sistematicamente, atualize a taxa de transferência.

## <a name="eventhuboutputinvalidconnectionconfig"></a>EventHubOutputInvalidConnectionConfig

* **Causa**: A configuração de conexão fornecida está incorreta.
* **Recomendação**: Corrija a configuração e reinicie o trabalho.

## <a name="eventhuboutputinvalidhostname"></a>EventHubOutputInvalidHostname

* **Causa**: O host do hub de eventos está inacessível.
* **Recomendação**: Verifique se o nome do host fornecido está correto.

## <a name="eventhuboutputunexpectedpartitioncount"></a>EventHubOutputUnexpectedPartitionCount

* **Causa**: O remetente do EventHub encontrou uma contagem inesperada de partições do EventHub.
* **Recomendação**: Reinicie o trabalho do Stream Analytics se a contagem de partições do EventHub for alterada.

## <a name="cosmosdbpartitionkeynotfound"></a>CosmosDBPartitionKeyNotFound

* **Causa**: O Stream Analytics não pôde localizar a chave de partição de uma coleção específica do Cosmos DB no banco de dados.
* **Recomendação**: Verifique se há uma chave de partição válida especificada para a coleção no Cosmos DB.

## <a name="cosmosdbinvalidpartitionkeycolumn"></a>CosmosDBInvalidPartitionKeyColumn

* **Causa**: Gerado quando uma chave de partição não é um nó folha nem está no nível superior.

## <a name="cosmosdbinvalididcolumn"></a>CosmosDBInvalidIdColumn

* **Causa**: A saída da consulta não poderá conter a coluna \[id] se uma coluna diferente for escolhida como a propriedade da chave primária.

## <a name="cosmosdbdatabasenotfound"></a>CosmosDBDatabaseNotFound

* **Causa**: O Stream Analytics não pode localizar um banco de dados do CosmosDB.

## <a name="cosmosdbcollectionnotfound"></a>CosmosDBCollectionNotFound

* **Causa**: O Stream Analytics não pode localizar uma coleção específica do Cosmos DB em um banco de dados.

## <a name="cosmosdboutputwritethrottling"></a>CosmosDBOutputWriteThrottling

* **Causa**: Erro ao gravar dados devido à limitação imposta pelo Cosmos DB.
* **Recomendação**: Atualize o nível de desempenho da coleção e ajuste o desempenho do banco de dados.

## <a name="sqldatabaseconnectionstringerror"></a>SQLDatabaseConnectionStringError

* **Causa**: O trabalho do Stream Analytics encontrou um erro de autenticação.
* **Recomendação**: Verifique se a cadeia de conexão do Banco de Dados SQL está correta.

## <a name="sqldatabasemanagedidentityauthenticationerror"></a>SQLDatabaseManagedIdentityAuthenticationError

* **Causa**: O trabalho do Stream Analytics encontrou um erro de autenticação. 
* **Recomendação**: Verifique se o nome da conta está configurado corretamente e se a identidade gerenciada do trabalho tem acesso ao Banco de Dados SQL.

## <a name="sqldatabaseoutputnotableerror"></a>SQLDatabaseOutputNoTableError

* **Causa**: O Stream Analytics não pode localizar as informações de esquema de uma tabela específica.

## <a name="sqldwoutputinvalidserviceedition"></a>SQLDWOutputInvalidServiceEdition

* **Causa**: Não há suporte para o Banco de Dados SQL.
* **Recomendação**: Use o pool de SQL dedicado.

## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas de conexões de entrada](stream-analytics-troubleshoot-input.md)
* [Solucionar problemas de saídas do Azure Stream Analytics](stream-analytics-troubleshoot-output.md)
* [Solucionar problemas de consultas do Azure Stream Analytics](stream-analytics-troubleshoot-query.md)
* [Solucionar problemas do Azure Stream Analytics usando logs de recursos](stream-analytics-job-diagnostic-logs.md)
* [Erros de dados do Azure Stream Analytics](data-errors.md)
