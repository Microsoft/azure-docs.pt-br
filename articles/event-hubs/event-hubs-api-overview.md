---
title: Visão geral da API dos Hubs de Eventos do Azure | Microsoft Docs
description: Visão geral das APIs de Hubs de Eventos do Azure disponíveis
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 3f221a0c-182d-4e39-9f3d-3a3c16c5c6ed
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2018
ms.author: shvija
ms.openlocfilehash: 80566b0246179064d2a479b8c9bf3c79a2a93aac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822629"
---
# <a name="available-event-hubs-apis"></a>APIs de Hubs de Eventos disponíveis

Este artigo descreve o conjunto de clientes disponíveis da API, que você pode usar para gerenciar recursos de Hubs de Eventos.

## <a name="runtime-apis"></a>APIs de tempo de execução

A seção a seguir descreve todos os clientes de tempo de execução dos Hubs de Eventos do Azure atualmente disponíveis. Embora algumas dessas bibliotecas também incluem a funcionalidade de gerenciamento limitado, também há [bibliotecas específicas](#management-apis) dedicada às operações de gerenciamento. O foco principal dessas bibliotecas é enviar e receber mensagens de um hub de eventos.

Para obter mais detalhes sobre o status atual de cada biblioteca de tempo de execução, veja [informações adicionais](#additional-information).

| Linguagem/plataforma | Pacote de cliente | Pacote EventProcessorHost | Repositório |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET Framework | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | N/D |
| Java | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| Nó | [NPM](https://www.npmjs.com/package/azure-event-hubs) | N/D | [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) |
| C | N/D | N/D | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>Informações adicionais

#### <a name="net"></a>.NET

O ecossistema do .NET tem vários tempos de execução, portanto, há várias bibliotecas .NET de Hubs de Eventos. A biblioteca .NET Standard pode ser executada usando o .NET Core ou o .NET Framework, enquanto a biblioteca do .NET Framework só pode ser executada em um ambiente do .NET Framework. Para saber mais sobre versões do .NET Framework, veja [versões da estrutura](https://docs.microsoft.com/dotnet/articles/standard/frameworks).

#### <a name="node"></a>Nó

A [biblioteca do Node.js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs) está atualmente em visualização e é mantida como um projeto de lado por funcionários da Microsoft e colaboradores externos. Todas as contribuições, incluindo código-fonte são bem-vindas e serão analisadas.

## <a name="management-apis"></a>APIs de gerenciamento

A seguir está uma lista de todas as bibliotecas específicas de gerenciamento disponíveis no momento. Nenhuma dessas bibliotecas contém operações de tempo de execução e elas têm o único propósito de gerenciar as entidades de Hubs de Eventos.

| Linguagem/plataforma | Pacote de gerenciamento | Repositório |
| --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>Próximas etapas
Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

* [Visão geral de Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Criar um hub de eventos](event-hubs-create.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)
