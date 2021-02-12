---
title: incluir arquivo
description: incluir arquivo
services: service-bus-messaging, event-hubs
author: spelluru
ms.service: service-bus-messaging, event-hubs
ms.topic: include
ms.date: 12/12/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 9bc641d680d927c44814f6814ebf6a6dde958c9e
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900752"
---
O Azure Functions permite a criação de tarefas de replicação somente de configuração que proenxutom em um ponto de entrada predefinido. Os [exemplos de replicação baseada em configuração para Azure Functions](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config) ilustram como aproveitar os [auxiliares pré-criados](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) em seu próprio código ou evitar manipular completamente o código e apenas usar a configuração.

## <a name="create-a-replication-task"></a>Criar uma tarefa de replicação
Para criar essa tarefa de replicação, primeiro crie uma nova pasta sob a pasta do projeto. O nome da nova pasta é o nome da função, por exemplo, `europeToAsia` ou `telemetry` . O nome não tem nenhuma correlação direta com as entidades de mensagens que estão sendo usadas e serve apenas para você identificá-las. Você pode criar dezenas de funções no mesmo projeto.

Em seguida, crie um `function.json` arquivo na pasta. O arquivo configura a função. Comece com o seguinte conteúdo:

``` JSON
{
    "configurationSource": "config",
    "bindings" : [
        {
            "direction": "in",
            "name": "input" 
        },
        {
            "direction": "out",
            "name": "output"
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": -1,
        "minimumInterval": "00:00:05",
        "maximumInterval": "00:05:00"
    },
    "disabled": false,
    "scriptFile": "../bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.*"
}
```

Nesse arquivo, você precisa concluir três etapas de configuração que dependem de quais entidades você deseja conectar:

1. [Configurar a direção de entrada](#configure-the-input-direction)
2. [Configurar a direção de saída](#configure-the-output-direction)
3. [Configurar o ponto de entrada](#configure-the-entry-point)

### <a name="configure-the-input-direction"></a>Configurar a direção de entrada

#### <a name="event-hub-input"></a>Entrada do hub de eventos

Se você quiser receber eventos de um hub de eventos, adicione as informações de configuração à seção superior em "associações" que o define

* **tipo** – o tipo "eventHubTrigger".
* **conexão** -o nome do valor das configurações do aplicativo para a cadeia de conexão do hub de eventos. 
* **eventHubName** -o nome do hub de eventos dentro do namespace identificado pela cadeia de conexão.
* grupo de **consumidores** -o nome de um dos grupos de consumidores. Lembre-se de que o nome está entre sinais de porcentagem e, portanto, também se refere a um valor de configurações de aplicativo.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "eventHubTrigger",
            "connection": "functionname-source-connection",
            "eventHubName": "eventHubA",
            "consumerGroup" : "%functionname-source-consumergroup%",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-queue-input"></a>Entrada da fila do barramento de serviço

Se você quiser receber eventos de uma fila do barramento de serviço, adicione informações de configuração à seção superior em "associações" que define

* **tipo** – o tipo "serviceBusTrigger".
* **Connection** -o nome do valor das configurações do aplicativo para a cadeia de conexão do barramento de serviço.
* **QueueName** – o nome da fila do barramento de serviço dentro do namespace identificado pela cadeia de conexão.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "queueName": "queue-a",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-topic-input"></a>Entrada de tópico do barramento de serviço

Se você quiser receber eventos de um tópico do barramento de serviço, adicione informações de configuração à seção superior em "associações" que define

* **tipo** – o tipo "serviceBusTrigger".
* **Connection** -o nome do valor das configurações do aplicativo para a cadeia de conexão do barramento de serviço. Esse valor deve ser `{FunctionName}-source-connection` se você quiser usar os scripts fornecidos.
* **topicname** -o nome do tópico do barramento de serviço dentro do namespace identificado pela cadeia de conexão.
* **subscriptionname** -o nome da assinatura do barramento de serviço no tópico fornecido no namespace identificado pela cadeia de conexão.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "topicName": "topic-x",
            "subscriptionName" : "sub-y",
            "name": "input" 
        }
    ...
```

### <a name="configure-the-output-direction"></a>Configurar a direção de saída

#### <a name="event-hub-output"></a>Saída do hub de eventos

Se você quiser encaminhar eventos para um hub de eventos, adicione informações de configuração à seção inferior dentro de "associações" que os conjuntos

* **tipo** – o tipo "eventHub".
* **conexão** -o nome do valor das configurações do aplicativo para a cadeia de conexão do hub de eventos. 
* **eventHubName** -o nome do hub de eventos dentro do namespace identificado pela cadeia de conexão.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "eventHub",
            "connection": "functionname-target-connection",
            "eventHubName": "eventHubB",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-queue-output"></a>Saída da fila do barramento de serviço

Se você quiser encaminhar eventos para uma fila do barramento de serviço, adicione informações de configuração à seção inferior dentro de "associações" que define

* **tipo** – o tipo "serviceBus".
* **Connection** -o nome do valor das configurações do aplicativo para a cadeia de conexão do barramento de serviço. 
* **QueueName** – o nome da fila do barramento de serviço dentro do namespace identificado pela cadeia de conexão.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "queueName": "queue-b",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-topic-output"></a>Saída do tópico do barramento de serviço

Se você quiser encaminhar eventos para um tópico do barramento de serviço, adicione informações de configuração à seção inferior dentro de "associações" que define

* **tipo** – o tipo "serviceBus".
* **Connection** -o nome do valor das configurações do aplicativo para a cadeia de conexão do barramento de serviço. 
* **topicname** -o nome do tópico do barramento de serviço dentro do namespace identificado pela cadeia de conexão.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "topicName": "topic-b",
            "name": "output" 
        }
    ...
```

### <a name="configure-the-entry-point"></a>Configurar o ponto de entrada

A configuração de ponto de entrada escolhe uma das tarefas de replicação padrão. Se você estiver modificando o `Azure.Messaging.Replication` projeto, também poderá adicionar tarefas e consultá-las aqui. Por exemplo:

```JSON
    ...
    "scriptFile": "../dotnet/bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub"
    ...
```

A tabela a seguir fornece os valores corretos para combinações de origens e destinos:

| Fonte      | Destino      | Entry Point 
|-------------|-------------|------------------------------------------------------------------------
| Hub de evento   | Hub de evento   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| Hub de evento   | Barramento de Serviço | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Barramento de Serviço | Hub de evento   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Barramento de Serviço | Barramento de Serviço | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`

### <a name="retry-policy"></a>Política de Repetição

Consulte a [documentação do Azure Functions em repetições](../articles/azure-functions/functions-bindings-error-pages.md) para configurar a política de repetição. As configurações de política escolhidas em todos os projetos neste repositório configuram uma estratégia de retirada exponencial com intervalos de repetição de 5 segundos a 5 minutos com tentativas infinitas para evitar a perda de dados.

Para o barramento de serviço, examine a seção ["usando o suporte de repetição na parte superior da resiliência do gatilho"](../articles/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience) para entender a interação de gatilhos e a contagem máxima de entrega definida para a fila.

### <a name="build-deploy-and-configure"></a>Criar, implantar e configurar

Embora você esteja concentrado na configuração, as tarefas ainda exigem a criação de um aplicativo implantável e a configuração dos hosts Azure Functions de forma que ele tenha todas as informações necessárias para se conectar aos pontos de extremidade fornecidos. 

Isso é ilustrado, junto com scripts reutilizáveis, nos [exemplos de replicação baseada em configuração para Azure Functions](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config).