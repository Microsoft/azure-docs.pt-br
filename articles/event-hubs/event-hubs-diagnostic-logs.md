---
title: Configurar logs de diagnóstico – Hub de Eventos do Azure | Microsoft Docs
description: Saiba como configurar logs de atividade e de diagnóstico para Hubs de Eventos no Azure.
ms.topic: article
ms.date: 02/25/2021
ms.openlocfilehash: 5067a2962693ee1c1955aa90e61b43358495585a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602597"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Configurar logs de diagnóstico para um hub de eventos do Azure

É possível exibir dois tipos de logs para os Hubs de Eventos do Azure:

* **[Logs de atividade](../azure-monitor/essentials/platform-logs-overview.md)** : Esses logs contém informações sobre as operações feitas em um trabalho. Os logs estão sempre habilitados. Você pode ver as entradas do log de atividades selecionando **Log de atividades** no painel esquerdo do seu namespace do hub de eventos no portal do Azure. Por exemplo:  "Criar ou atualizar o namespace", "Criar ou atualizar hub de eventos".

    ![Log de atividades para um namespace de Hubs de Eventos](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[Logs de diagnóstico](../azure-monitor/essentials/platform-logs-overview.md)** : Os logs de diagnóstico fornecem informações mais detalhadas sobre operações e ações que são conduzidas no seu namespace usando a API, ou através de clientes de gerenciamento no SDK de linguagem. 
    
    A seção a seguir mostra como habilitar os logs de diagnóstico para um namespace de Hubs de Eventos.

## <a name="enable-diagnostic-logs"></a>Habilitar logs de diagnóstico
Os logs de diagnóstico estão desabilitados por padrão. Para habilitar logs de diagnóstico, siga estas etapas:

1.  No [Portal do Azure](https://portal.azure.com), navegue até o seu namespace de Hubs de Eventos. 
2. Selecione **Configurações de diagnóstico** em **Monitoramento** no painel esquerdo e, em seguida, selecione **+Adicionar configuração de diagnóstico**. 

    ![Página Configurações de diagnóstico - adicionar configuração de diagnóstico](./media/event-hubs-diagnostic-logs/diagnostic-settings-page.png)
4. Na seção **Detalhes da categoria**, selecione os **Tipos de logs de diagnóstico** que você deseja habilitar. Você encontrará detalhes sobre essas categorias posteriormente neste artigo. 
5. Na seção **Detalhes de destino**, defina o destino do arquivo (destino) desejado; por exemplo, uma conta de armazenamento, um hub de eventos ou um espaço de trabalho Log Analytics.

    ![Página Adicionar configurações de diagnóstico](./media/event-hubs-diagnostic-logs/aDD-diagnostic-settings-page.png)
6.  Selecione **Salvar** na barra de ferramentas para salvar as configurações de diagnóstico.

    As novas configurações terão efeito em aproximadamente 10 minutos. Depois disso, os logs aparecerão no destino de arquivamento configurado, no painel **Logs de diagnóstico**.

    Para saber mais sobre como configurar um diagnóstico, confira a [visão geral dos logs de diagnóstico do Azure](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="diagnostic-logs-categories"></a>Categorias dos logs de diagnóstico

Os Hubs de Eventos capturam os logs de diagnóstico das seguintes categorias:

| Categoria | Descrição | 
| -------- | ----------- | 
| Logs de arquivo | Captura informações sobre operações de [Captura de Hubs de Eventos](event-hubs-capture-overview.md), especificamente, logs relacionados a erros de captura. |
| Logs operacionais | Captura todas as operações de gerenciamento executadas no namespace dos Hubs de Eventos do Azure. As operações de dados não são capturadas devido ao alto volume de operações de dados que são realizadas nos hubs de eventos do Azure. |
| Logs de dimensionamento automático | Captura operações de inflação automática realizada em um namespace de Hubs de Eventos. |
| Logs do coordenador de Kafka | Captura operações de coordenador de Kafka relacionadas aos Hubs de Eventos. |
| Logs de erros do usuário do Kafka | Captura informações sobre APIs do Kafka chamadas em Hubs de Eventos. |
| Evento de conexão de rede virtual de Hubs de Eventos (VNet) | Captura informações sobre endereços IP e redes virtuais enviando tráfego para os Hubs de Eventos. |
| Logs de usuário de chave gerenciada pelo cliente | Captura operações relacionadas à chave gerenciada pelo cliente. |


Todos os logs são armazenados no formato JSON (JavaScript Object Notation). Cada entrada tem campos de cadeia de caracteres que usam o formato descrito nas seções a seguir.

## <a name="archive-logs-schema"></a>Esquema dos logs de arquivo morto

As cadeias de caracteres JSON do log de arquivo morto incluem os elementos listados na seguinte tabela:

Nome | Descrição
------- | -------
`TaskName` | Descrição da tarefa que falhou
`ActivityId` | ID interna, usada para acompanhamento
`trackingId` | ID interna, usada para acompanhamento
`resourceId` | ID de recurso do Azure Resource Manager
`eventHub` | Nome completo do hub de eventos (inclui o nome do namespace)
`partitionId` | Partição do Hub de Eventos usada para gravação
`archiveStep` | valores possíveis: ArchiveFlushWriter, DestinationInit
`startTime` | Hora de início de falha
`failures` | Número de vezes que a falha ocorreu
`durationInSeconds` | Duração de falha
`message` | Mensagem de erro
`category` | ArchiveLogs

O código a seguir é um exemplo de uma cadeia de caracteres JSON do log de arquivo morto:

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "000000000-0000-0000-0000-0000000000000",
   "trackingId": "0000000-0000-0000-0000-00000000000000000",
   "resourceId": "/SUBSCRIPTIONS/000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs Namespace Name>",
   "eventHub": "<Event Hub full name>",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```

## <a name="operational-logs-schema"></a>Esquema de logs operacionais

As cadeias de caracteres JSON do log operacional incluem os elementos listados na seguinte tabela:

Nome | Descrição
------- | -------
`ActivityId` | ID interna, usada para acompanhamento |
`EventName` | Nome da operação. Para obter uma lista de valores para esse elemento, consulte os [nomes de evento](#event-names) |
`resourceId` | ID de recurso do Azure Resource Manager |
`SubscriptionId` | ID da assinatura |
`EventTimeString` | Tempo de operação |
`EventProperties` |Propriedades da operação. Esse elemento fornece mais informações sobre o evento, conforme mostrado no exemplo a seguir. |
`Status` | Status da operação. O valor pode ser **bem-sucedido** ou ter **falhado**.  |
`Caller` | Chamador da operação (portal do Azure ou cliente de gerenciamento) |
`Category` | OperationalLogs |

O código a seguir é um exemplo de uma cadeia de caracteres JSON do log operacional:

```json
Example:
{
   "ActivityId": "00000000-0000-0000-0000-00000000000000",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs namespace name>",
   "SubscriptionId": "000000000-0000-0000-0000-000000000000",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"0000000000-0000-0000-0000-000000000000\",\"Namespace\":\"<Namespace Name>\",\"Via\":\"https://<Namespace Name>.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

### <a name="event-names"></a>Nomes de evento
O nome do evento é preenchido como tipo de operação + tipo de recurso das enumerações a seguir. Por exemplo, `Create Queue`, `Retrieve Event Hu` ou `Delete Rule`. 

| Tipo de operação | Tipo de recurso | 
| -------------- | ------------- | 
| <ul><li>Criar</li><li>Atualizar</li><li>Excluir</li><li>Recuperar</li><li>Desconhecido</li></ul> | <ul><li>Namespace</li><li>Fila</li><li>Tópico</li><li>Assinatura</li><li>EventHub</li><li>EventHubSubscription</li><li>NotificationHub</li><li>NotificationHubTier</li><li>SharedAccessPolicy</li><li>UsageCredit</li><li>NamespacePnsCredentials</li>Regra</li>ConsumerGroup</li> |

## <a name="autoscale-logs-schema"></a>Esquema dos logs de dimensionamento automático
O JSON do log de dimensionamento automático incluem os elementos listados na seguinte tabela:

| Nome | Descrição |
| ---- | ----------- | 
| `TrackingId` | ID interna, que é usada para fins de rastreamento |
| `ResourceId` | ID do Recurso do Azure Resource Manager. |
| `Message` | Mensagem informativa, que fornece detalhes sobre a ação de inflar automaticamente. A mensagem contém o valor anterior e o atual da unidade de taxa de transferência para um namespace específico e o que disparou a inflação da TU. |

Veja um exemplo de evento de dimensionamento automático: 

```json
{
    "TrackingId": "fb1b3676-bb2d-4b17-85b7-be1c7aa1967e",
    "Message": "Scaled-up EventHub TUs (UpdateStartTimeUTC: 5/13/2020 7:48:36 AM, PreviousValue: 1, UpdatedThroughputUnitValue: 2, AutoScaleReason: 'IncomingMessagesPerSecond reached 2170')",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name"
}
```

## <a name="kafka-coordinator-logs-schema"></a>Esquema de logs do coordenador de Kafka
O JSON do log do coordenador de Kafka inclui os elementos listados na seguinte tabela:

| Nome | Descrição |
| ---- | ----------- | 
| `RequestId` | ID de solicitação, que é usada para fins de rastreamento |
| `ResourceId` | ID de recurso do Azure Resource Manager |
| `Operation` | Nome da operação que é feita durante a coordenação do grupo |
| `ClientId` | ID do Cliente |
| `NamespaceName` | Nome do Namespace | 
| `SubscriptionId` | ID da assinatura do Azure |
| `Message` | Mensagem informativa ou de aviso, que fornece detalhes sobre as ações feitas durante a coordenação do grupo. |

### <a name="example"></a>Exemplo

```json
{
    "RequestId": "FE01001A89E30B020000000304620E2A_KafkaExampleConsumer#0",
    "Operation": "Join.Start",
    "ClientId": "KafkaExampleConsumer#0",
    "Message": "Start join group for new member namespace-name:c:$default:I:KafkaExampleConsumer#0-cc40856f7f3c4607915a571efe994e82, current group size: 0, API version: 2, session timeout: 10000ms, rebalance timeout: 300000ms.",
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "KafkaCoordinatorLogs"
}
```

## <a name="kafka-user-error-logs-schema"></a>Esquema de logs de erros do usuário do Kafka
O JSON do log de erro do usuário do Kafka inclui os elementos listados na seguinte tabela:

| Nome | Descrição |
| ---- | ----------- |
| `TrackingId` | ID de rastreamento, que é usada para fins de rastreamento. |
| `NamespaceName` | Nome do Namespace |
| `Eventhub` | Nome do Hub de Eventos |
| `PartitionId` | Partition ID |
| `GroupId` | ID do Grupo |
| `ClientId` | ID do Cliente |
| `ResourceId` | ID do Recurso do Azure Resource Manager. |
| `Message` | Mensagem informativa, que fornece detalhes sobre um erro |

## <a name="event-hubs-virtual-network-connection-event-schema"></a>Esquema de evento de conexão de rede virtual de Hubs de Eventos
O evento JSON de conexão de VNet (rede virtual) dos Hubs de Eventos inclui elementos listados na tabela a seguir:

| Nome | Descrição |
| ---  | ----------- | 
| `SubscriptionId` | ID da assinatura do Azure |
| `NamespaceName` | Nome do Namespace |
| `IPAddress` | Endereço IP de um cliente que se conecta ao serviço de Hubs de Eventos |
| `Action` | Ação feita pelo serviço de Hubs de Eventos ao avaliar solicitações de conexão. As ações com suporte são **Aceitar conexão** e **Negar conexão**. |
| `Reason` | Fornece um motivo pelo qual a ação foi feita |
| `Count` | Número de ocorrências para a ação especificada |
| `ResourceId` | ID do Recurso do Azure Resource Manager. |

Os logs de rede virtual serão gerados somente se o namespace permitir o acesso de **redes selecionadas** ou de **endereços IP específicos** (regras de filtro IP). Se você não quiser restringir o acesso ao seu namespace usando esses recursos e ainda desejar obter logs de rede virtual para rastrear endereços IP de clientes que se conectam ao namespace de hubs de eventos, você pode usar a seguinte solução alternativa. [Habilite a filtragem de IP](event-hubs-ip-filtering.md)e adicione o intervalo de IPv4 endereçável total (1.0.0.0/1-255.0.0.0/1). A filtragem de IP dos hubs de eventos não dá suporte a intervalos de IPv6. Observe que você pode ver endereços de ponto de extremidade privados no formato IPv6 no log. 

### <a name="example"></a>Exemplo

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

## <a name="customer-managed-key-user-logs"></a>Logs de usuário de chave gerenciada pelo cliente
O JSON do log de usuário gerenciada pelo cliente inclui os elementos listados na seguinte tabela:

| Nome | Descrição |
| ---- | ----------- | 
| `Category` | Tipo de categoria para uma mensagem. É um dos seguintes valores: **error** e **info** |
| `ResourceId` | ID de recurso interno, que inclui a ID da assinatura do Azure e o nome do namespace |
| `KeyVault` | Nome do recurso do Key Vault |
| `Key` | Nome da chave do Key Vault. |
| `Version` | Versão da chave do Key Vault |
| `Operation` | O nome de uma operação feita para atender a solicitações |
| `Code` | Código de status |
| `Message` | Mensagem, que fornece detalhes sobre um erro ou mensagem informativa |



## <a name="next-steps"></a>Próximas etapas
- [Introdução aos Hubs de Eventos](./event-hubs-about.md)
- [Exemplos de Hubs de Eventos](sdks.md)
- Introdução aos Hubs de Eventos
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-node-get-started-send.md)
