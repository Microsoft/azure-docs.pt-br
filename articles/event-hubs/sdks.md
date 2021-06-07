---
title: Hubs de eventos do Azure-SDKs do cliente | Microsoft Docs
description: Este artigo fornece informações sobre SDKs de cliente para hubs de eventos do Azure.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 9f4eec603245f1e4ea6fa4d97b843ca6a770f2ad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88930869"
---
# <a name="azure-event-hubs---client-sdks"></a>Hubs de eventos do Azure-SDKs de cliente
Este artigo fornece as seguintes informações para os SDKs com suporte dos hubs de eventos do Azure: 

- Local do pacote que você pode usar em seus aplicativos 
- Local do GitHub onde você pode encontrar código-fonte, exemplos, leiame, log de alterações, problemas relatados e também gerar novos problemas 
- Links para tutoriais de início rápido 

## <a name="client-sdks"></a>SDKs do cliente
A tabela a seguir descreve todos os clientes de tempo de execução dos hubs de eventos do Azure disponíveis atualmente. Embora algumas dessas bibliotecas também incluem a funcionalidade de gerenciamento limitado, também há bibliotecas específicas dedicada às operações de gerenciamento. O foco principal dessas bibliotecas é **Enviar e receber mensagens** de um hub de eventos.

| Linguagem | Pacote | Referência | 
| -------- | ------- | --------------- | 
| . NET Standard (**mais recente** e dá suporte ao .NET Core e ao .NET Framework) | [Azure. Messaging. EventHubs](https://www.nuget.org/packages/Azure.Messaging.EventHubs/) |<ul><li>[Local do GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs)</li><li>[Tutorial](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
|       | [Azure. Messaging. EventHubs. Processor](https://www.nuget.org/packages/Azure.Messaging.EventHubs.Processor/) | <ul><li>[Local do GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor)</li><li>[Tutorial](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| . NET Standard (**herdado** e compatível com .NET Core e .NET Framework) | [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | <ul><li>[Local do GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs)</li><li>[Tutorial](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> | 
|       | [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor) | <ul><li>[Local do GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.Processor)</li><li>[Tutorial](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| . .NET Framework (**antigo**) | [WindowsAzure.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) |<ul><li>[Tutorial](event-hubs-dotnet-framework-getstarted-send.md)</li></ul> |
| Java | [azure-messaging-eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs) | <ul><li>[Local do GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs)</li><li>[Tutorial](event-hubs-java-get-started-send.md)</li></ul> |
|      | [Azure-Eventhubs](https://search.maven.org/search?q=a:azure-eventhubs) **(Herdado)** | <ul><li>[Local do GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/microsoft-azure-eventhubs)</li><li>[Tutorial](event-hubs-java-get-started-send.md)</li></ul> |
| Python |  [azure-eventhub](https://pypi.org/project/azure-eventhub/) | <ul><li>[Local do GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub)</li><li>[Tutorial](event-hubs-python-get-started-send.md)</li></ul> |
|        | [azure-eventhub-checkpointstoreblob-aio](https://pypi.org/project/azure-eventhub-checkpointstoreblob-aio/) | <ul><li>[Local do GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio)</li><li>[Tutorial](event-hubs-python-get-started-send.md)</li></ul> |
| JavaScript | [Azure/hubs de eventos](https://www.npmjs.com/package/@azure/event-hubs) | <ul><li>[Local do GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs)</li><li>[Tutorial](event-hubs-node-get-started-send.md)</li></ul> |
|            | [Azure/Eventhubs-checkpointstore-blob](https://www.npmjs.com/package/@azure/eventhubs-checkpointstore-blob) | <ul><li>[Local do GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/eventhubs-checkpointstore-blob)</li><li>[Tutorial](event-hubs-node-get-started-send.md)</li></ul> |
| Go | [Azure-eventos-hubs-go](https://github.com/Azure/azure-event-hubs-go) | <ul><li>[Local do GitHub](https://github.com/Azure/azure-event-hubs-go)</li><li>[Tutorial](event-hubs-go-get-started-send.md)</li></ul> |
| C | [Azure-Event-hubs-c](https://github.com/Azure/azure-event-hubs-c) | <ul><li>[Local do GitHub](https://github.com/Azure/azure-event-hubs-c)</li><li>[Tutorial](event-hubs-c-getstarted-send.md)</li></ul> |

## <a name="management-sdks"></a>SDKs de gerenciamento
A seguir está uma lista de todas as bibliotecas específicas de gerenciamento disponíveis no momento. Nenhuma dessas bibliotecas contém operações de tempo de execução e são para o único propósito de **gerenciar entidades de hubs de eventos**.

| Linguagem | Pacote | Referência | 
| -------- | ------- | --------------- | 
| .NET Standard | [Microsoft.Azure.Management.EventHub](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) |<ul><li>[Local do GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.Management.EventHub)</li><li>[Tutorial](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |


## <a name="next-steps"></a>Próximas etapas

Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

* [Visão geral de Hubs de Eventos](./event-hubs-about.md)
* [Criar um Hub de Eventos](event-hubs-create.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)
