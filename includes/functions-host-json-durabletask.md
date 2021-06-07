---
title: incluir arquivo
description: incluir arquivo
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d1b2cbefe64add5048d4e3c7946277b1461a5434
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607075"
---
Definições de configuração para [Funções Duráveis](../articles/azure-functions/durable/durable-functions-overview.md).

> [!NOTE]
> Todas as versões principais das Durable Functions são compatíveis com todas as versões do Azure Functions Runtime. No entanto, o esquema da configuração host.json é ligeiramente diferente dependendo da versão do Azure Functions Runtime e da versão da extensão das Durable Functions que você usa. Os exemplos a seguir são referentes ao uso com o Azure Functions 2.0 e 3.0. Em ambos os exemplos, se você estiver usando o Azure Functions 1.0, as configurações disponíveis serão as mesmas, mas a seção "durableTask" do host.json deverá estar na raiz da configuração host.json, em vez de como um campo em "extensões".

### <a name="durable-functions-2x"></a><a name="durable-functions-2-0-host-json"></a>Durable Functions 2.x

```json
{
 "extensions": {
  "durableTask": {
    "hubName": "MyTaskHub",
    "storageProvider": {
      "connectionStringName": "AzureWebJobsStorage",
      "controlQueueBatchSize": 32,
      "controlQueueBufferThreshold": 256,
      "controlQueueVisibilityTimeout": "00:05:00",
      "maxQueuePollingInterval": "00:00:30",
      "partitionCount": 4,
      "trackingStoreConnectionStringName": "TrackingStorage",
      "trackingStoreNamePrefix": "DurableTask",
      "useLegacyPartitionManagement": true,
      "workItemQueueVisibilityTimeout": "00:05:00",
    },
    "tracing": {
      "traceInputsAndOutputs": false,
      "traceReplayEvents": false,
    },
    "notifications": {
      "eventGrid": {
        "topicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
        "keySettingName": "EventGridKey",
        "publishRetryCount": 3,
        "publishRetryInterval": "00:00:30",
        "publishEventTypes": [
          "Started",
          "Pending",
          "Failed",
          "Terminated"
        ]
      }
    },
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "extendedSessionsEnabled": false,
    "extendedSessionIdleTimeoutInSeconds": 30,
    "useAppLease": true,
    "useGracefulShutdown": false
  }
 }
}

```

### <a name="durable-functions-1x"></a>Durable Functions 1.x

```json
{
  "extensions": {
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
      "eventGridPublishEventTypes": ["Started", "Completed", "Failed", "Terminated"]
    }
  }
}
```

Nomes de hubs de tarefas devem começar com uma letra e devem ser compostos somente por letras e números. Se não for especificado, o nome do hub de tarefas padrão de um aplicativo de funções será **DurableFunctionsHub**. Para obter mais informações, consulte [Hubs de tarefas](../articles/azure-functions/durable/durable-functions-task-hubs.md).

|Propriedade  |Padrão | Descrição |
|---------|---------|----------|
|hubName|DurableFunctionsHub|Nomes alternativos para [hub de tarefas](../articles/azure-functions/durable/durable-functions-task-hubs.md) podem ser usados para isolar vários aplicativos de Funções Duráveis uns dos outros, mesmo se eles estiverem usando o mesmo back-end de armazenamento.|
|controlQueueBatchSize|32|O número de mensagens para efetuar pull da fila de controle por vez.|
|controlQueueBufferThreshold| **Plano de consumo**: 32 <br> **Plano Dedicado/Premium**: 256 |O número de mensagens de fila de controle que podem ser armazenadas em buffer na memória por vez. Nesse ponto, o dispatcher aguardará antes de retirar da fila qualquer mensagem adicional.|
|partitionCount |4|A contagem de partição para a fila de controle. Pode ser um número inteiro positivo entre 1 e 16.|
|controlQueueVisibilityTimeout |5 minutos|O limite de tempo de visibilidade das mensagens de remoção da fila de controle.|
|workItemQueueVisibilityTimeout |5 minutos|O limite de tempo de visibilidade das mensagens de remoção da fila de item de trabalho.|
|maxConcurrentActivityFunctions | **Plano de Consumo**: 10 <br> **Plano Dedicado/Premium**: dez vezes o número de processadores do computador atual|O número máximo de funções de atividade que podem ser processadas simultaneamente em uma única instância de host.|
|maxConcurrentOrchestratorFunctions | **Plano de Consumo**: 5 <br> **Plano Dedicado/Premium**: dez vezes o número de processadores do computador atual |O número máximo de funções do orquestrador que podem ser processadas simultaneamente em uma única instância do host.|
|maxQueuePollingInterval|30 segundos|O intervalo de sondagem da fila de item de trabalho e de controle máximo no formato *hh:mm:ss*. Valores mais altos podem resultar em latências de processamento de mensagens mais altas. Valores mais baixos podem resultar em custos de armazenamento maiores devido a um maior número de transações de armazenamento.|
|azureStorageConnectionStringName |AzureWebJobsStorage|O nome da configuração de aplicativo que tem a cadeia de conexão do Armazenamento do Azure usada para gerenciar os recursos subjacentes do Armazenamento do Azure.|
|trackingStoreConnectionStringName||O nome de uma cadeia de conexão a ser usada para as tabelas Histórico e Instâncias. Se não for especificado, a conexão `connectionStringName` (Durable 2.x) ou `azureStorageConnectionStringName` (Durable 1.x) será usada.|
|trackingStoreNamePrefix||O prefixo a ser usado para as tabelas Histórico e Instâncias quando `trackingStoreConnectionStringName` for especificado. Se não estiver definido, o valor de prefixo padrão será `DurableTask`. Se `trackingStoreConnectionStringName` não for especificado, as tabelas Histórico e Instâncias usarão o valor `hubName` como o prefixo e qualquer configuração de `trackingStoreNamePrefix` será ignorada.|
|traceInputsAndOutputs |false|Um valor que indica se as entradas e saídas de chamadas de função sertão rastreadas. O comportamento padrão durante o rastreamento de eventos de execução de função é incluir o número de bytes nas entradas e saídas serializadas para chamadas de função. Esse comportamento fornece um mínimo de informações sobre como são as entradas e saídas sem sobrecarregar os logs ou expor inadvertidamente informações confidenciais. A definição dessa propriedade como true faz com que o log de função padrão registre todo o conteúdo de entradas e saídas da função.|
|logReplayEvents|false|Um valor que indica se é necessário gravar eventos de reprodução de orquestração para o Application Insights.|
|eventGridTopicEndpoint ||A URL de um ponto de extremidade de tópico personalizado da Grade de Eventos do Azure. Quando essa propriedade for definida, eventos de notificação de ciclo de vida de orquestração serão publicados para esse ponto de extremidade. Esta propriedade dá suporte à resolução de Configurações do Aplicativo.|
|eventGridKeySettingName ||O nome da configuração de aplicativo que contém a chave usada para autenticar com o tópico personalizado da Grade de Eventos do Azure em `EventGridTopicEndpoint`.|
|eventGridPublishRetryCount|0|O número de novas tentativas se a publicação no Tópico de Grade de Eventos falha.|
|eventGridPublishRetryInterval|5 minutos|A Grade de Eventos publica o intervalo de repetição no formato *hh:mm:ss*.|
|eventGridPublishEventTypes||Uma lista de tipos de eventos a serem publicados na Grade de Eventos. Se não for especificada, todos os tipos de evento serão publicados. Os valores permitidos incluem `Started`, `Completed`, `Failed`, `Terminated`.|
|useAppLease|true|Quando configurado como `true`, os aplicativos exigirão adquirir uma concessão de blob de nível de aplicativo antes de processar mensagens do hub de tarefas. Para obter mais informações, consulte a documentação [recuperação de desastre e distribuição geográfica](../articles/azure-functions/durable/durable-functions-disaster-recovery-geo-distribution.md). Disponível a partir da v2.3.0.
|useLegacyPartitionManagement|false|Quando definido como `false`, ele usa um algoritmo de gerenciamento de partição que reduz a possibilidade de execução de função duplicada ao expandir.  Disponível a partir da v2.3.0.|
|useGracefulShutdown|false|(Versão Prévia) Habilite o desligamento normal para reduzir a chance de desligamentos de host falharem em execuções de função em processo.|

Muitas dessas configurações servem para otimizar o desempenho. Para obter mais informações, consulte [Desempenho e escala](../articles/azure-functions/durable/durable-functions-perf-and-scale.md).
