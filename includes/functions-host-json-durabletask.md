---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d79d1bd5ec244ad4399a02c349e2504516d06ccd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60710516"
---
Definições de configuração para [Funções Duráveis](../articles/azure-functions/durable-functions-overview.md).

```json
{
  "durableTask": {
    "hubName": "MyTaskHub",
    "controlQueueBatchSize": 32,
    "partitionCount": 4,
    "controlQueueVisibilityTimeout": "00:05:00",
    "workItemQueueVisibilityTimeout": "00:05:00",
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "maxQueuePollingInterval": "00:00:30",
    "azureStorageConnectionStringName": "AzureWebJobsStorage",
    "trackingStoreConnectionStringName": "TrackingStorage",
    "trackingStoreNamePrefix": "DurableTask",
    "traceInputsAndOutputs": false,
    "logReplayEvents": false,
    "eventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName":  "EventGridKey",
    "eventGridPublishRetryCount": 3,
    "eventGridPublishRetryInterval": "00:00:30",
    "eventGridPublishEventTypes": ["Started", "Pending", "Failed", "Terminated"]
  }
}
```

Nomes de hubs de tarefas devem começar com uma letra e devem ser compostos somente por letras e números. Se não for especificado, o nome do hub de tarefas padrão de um aplicativo de funções será **DurableFunctionsHub**. Para obter mais informações, consulte [Hubs de tarefas](../articles/azure-functions/durable-functions-task-hubs.md).

|Propriedade  |Padrão | DESCRIÇÃO |
|---------|---------|---------|
|hubName|DurableFunctionsHub|Nomes alternativos para [hub de tarefas](../articles/azure-functions/durable-functions-task-hubs.md) podem ser usados para isolar vários aplicativos de Funções Duráveis uns dos outros, mesmo se eles estiverem usando o mesmo back-end de armazenamento.|
|controlQueueBatchSize|32|O número de mensagens para efetuar pull da fila de controle por vez.|
|partitionCount |4|A contagem de partição para a fila de controle. Pode ser um número inteiro positivo entre 1 e 16.|
|controlQueueVisibilityTimeout |5 minutos|O limite de tempo de visibilidade das mensagens de remoção da fila de controle.|
|workItemQueueVisibilityTimeout |5 minutos|O limite de tempo de visibilidade das mensagens de remoção da fila de item de trabalho.|
|maxConcurrentActivityFunctions |10 vezes o número de processadores no computador atual|O número máximo de funções de atividade que podem ser processadas simultaneamente em uma única instância de host.|
|maxConcurrentOrchestratorFunctions |10 vezes o número de processadores no computador atual|O número máximo de funções do orquestrador que podem ser processadas simultaneamente em uma única instância do host.|
|maxQueuePollingInterval|30 segundos|O controle máximo e o intervalo de sondagem de fila de item de trabalho na *hh* formato. Valores mais altos podem resultar em maior latências de processamento de mensagens. Valores mais baixos podem resultar em custos de armazenamento maiores devido a transações de armazenamento maior.|
|azureStorageConnectionStringName |AzureWebJobsStorage|O nome da configuração de aplicativo que tem a cadeia de conexão do Armazenamento do Azure usada para gerenciar os recursos subjacentes do Armazenamento do Azure.|
|trackingStoreConnectionStringName||O nome de uma cadeia de caracteres de conexão a ser usado para as tabelas de histórico e instâncias. Se não for especificado, o `azureStorageConnectionStringName` conexão é usada.|
|trackingStoreNamePrefix||O prefixo a ser usado para as instâncias e o histórico de tabelas quando `trackingStoreConnectionStringName` for especificado. Se não configurado, o valor de prefixo padrão será `DurableTask`. Se `trackingStoreConnectionStringName` não for especificado, em seguida, usarão as tabelas de histórico e instâncias de `hubName` valor como seu prefixo e qualquer configuração para `trackingStoreNamePrefix` será ignorado.|
|traceInputsAndOutputs |falso|Um valor que indica se as entradas e saídas de chamadas de função sertão rastreadas. O comportamento padrão durante o rastreamento de eventos de execução de função é incluir o número de bytes nas entradas e saídas serializadas para chamadas de função. Esse comportamento fornece algumas informações sobre as entradas e saídas como aparência sem sobrecarregar os logs ou inadvertidamente expor informações confidenciais. A definição dessa propriedade como true faz com que o log de função padrão registre todo o conteúdo de entradas e saídas da função.|
|logReplayEvents|falso|Um valor que indica se é necessário gravar eventos de reprodução de orquestração para o Application Insights.|
|eventGridTopicEndpoint ||A URL de um ponto de extremidade de tópico personalizado da Grade de Eventos do Azure. Quando essa propriedade é definida, os eventos de notificação de ciclo de vida de orquestração são publicados para esse ponto de extremidade. Esta propriedade dá suporte à resolução de Configurações do Aplicativo.|
|eventGridKeySettingName ||O nome da configuração de aplicativo que contém a chave usada para autenticar com o tópico personalizado da Grade de Eventos do Azure em `EventGridTopicEndpoint`.|
|eventGridPublishRetryCount|0|O número de novas tentativas se a publicação no Tópico de Grade de Eventos falha.|
|eventGridPublishRetryInterval|5 minutos|A Grade de Eventos publica o intervalo de repetição no formato *hh:mm:ss*.|
|eventGridPublishEventTypes||Uma lista de tipos de eventos para publicar na grade de eventos. Se não for especificado, todos os tipos de evento serão publicados. Permitido de valores incluem `Started`, `Completed`, `Failed`, `Terminated`.|

Muitas dessas configurações são para otimizar o desempenho. Para obter mais informações, consulte [Desempenho e escala](../articles/azure-functions/durable-functions-perf-and-scale.md).