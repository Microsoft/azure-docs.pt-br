---
title: Visão geral da API de Retransmissão do Azure | Microsoft Docs
description: Visão geral das APIs de Retransmissão do Azure disponíveis
services: event-hubs
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/02/2018
ms.author: spelluru
ms.openlocfilehash: 05d7ac56d6c1c48125eb458d0eee852ba396b300
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60593351"
---
# <a name="available-relay-apis"></a>APIs de Retransmissão disponíveis

## <a name="runtime-apis"></a>APIs de tempo de execução

A tabela a seguir lista todos os clientes de tempo de execução de Retransmissão disponíveis no momento.

A seção [informações adicionais](#additional-information) contém mais informações sobre o status de cada biblioteca de tempo de execução.

| Linguagem/plataforma | Recurso disponível | Pacote de cliente | Repositório |
| --- | --- | --- | --- |
| .NET Standard | Conexões Híbridas | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | Retransmissão de WCF | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | N/D |
| Nó | Conexões Híbridas | [Websockets: `hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[Websockets: `hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[Solicitações HTTP: `hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Informações adicionais

#### <a name="net"></a>.NET

O ecossistema .NET tem vários tempos de execução, portanto, há várias bibliotecas .NET para a Retransmissão. A biblioteca .NET Standard pode ser executada usando o .NET Core ou o .NET Framework, enquanto a biblioteca do .NET Framework só pode ser executada em um ambiente do .NET Framework. Para saber mais sobre o .NET Framework, veja [versões da estrutura](/dotnet/articles/standard/frameworks).

A biblioteca do .NET Framework dá suporte apenas ao modelo de programação do WCF e conta com um protocolo binário proprietário baseado no transporte WCF `net.tcp`. Este protocolo e biblioteca são mantidos para compatibilidade com aplicativos existentes.

A biblioteca do .NET Standard é baseada na definição de protocolo aberto para a Retransmissão de Conexões Híbridas com base em HTTP e WebSockets. A biblioteca dá suporte a uma abstração de fluxo sobre Websockets e um gesto de API de solicitação-resposta simples para responder a solicitações HTTP. O exemplo [API da Web](https://github.com/Azure/azure-relay-dotnet) mostra como integrar Conexões Híbridas com ASP.NET Core para serviços Web.

#### <a name="nodejs"></a>Node.js

Os módulos de Conexões Híbridas listados na tabela acima substituem ou corrigem módulos Node.js existentes por implementações alternativas que escutam no serviço de Retransmissão do Azure em vez da pilha de rede local.

O módulo `hyco-https` corrige e substitui parcialmente os módulos Node.js principais `http` e `https`, fornecendo uma implementação de ouvinte HTTPS que é compatível com muitos aplicativos de módulos Node.js existentes que dependem desses módulos principais.

Os módulos `hyco-ws` e `hyco-websocket` corrigem os módulos populares `ws` e `websocket` para Node.js, fornecendo implementações de ouvinte alternativas que permitem que os módulos e aplicativos dependentes de qualquer módulo trabalhem na Retransmissão de Conexões Híbridas.

Detalhes sobre esses módulos podem ser encontrados no repositório GitHub [azure-relay-node](https://github.com/Azure/azure-relay-node).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a Retransmissão do Azure, visite estes links:
* [O que é Retransmissão do Azure?](relay-what-is-it.md)
* [Perguntas frequentes sobre retransmissão](relay-faq.md)
