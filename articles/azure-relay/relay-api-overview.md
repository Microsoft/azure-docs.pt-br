---
title: Visão geral da API de Retransmissão do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral das APIs de Retransmissão do Azure disponíveis (.NET Standard, .NET Framework, Node.js e outros)
ms.topic: article
ms.custom: devx-track-dotnet
ms.date: 06/23/2020
ms.openlocfilehash: 391bd74f2bce8721b6d6359f3990af494277aa45
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312987"
---
# <a name="available-relay-apis"></a>APIs de Retransmissão disponíveis

## <a name="runtime-apis"></a>APIs de runtime

A tabela a seguir lista todos os clientes de runtime de Retransmissão disponíveis no momento.

A seção [informações adicionais](#additional-information) contém mais informações sobre o status de cada biblioteca de runtime.

| Linguagem/plataforma | Recurso disponível | Pacote de cliente | Repositório |
| --- | --- | --- | --- |
| .NET Standard | Conexões Híbridas | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | Retransmissão de WCF | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | N/D |
| Nó | Conexões Híbridas | [WebSockets: `hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[WebSockets: `hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[Solicitações HTTP: `hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Informações adicionais

#### <a name="net"></a>.NET

O ecossistema .NET tem vários runtimes, portanto, há várias bibliotecas .NET para a Retransmissão. A biblioteca .NET Standard pode ser executada usando o .NET Core ou o .NET Framework, enquanto a biblioteca do .NET Framework só pode ser executada em um ambiente do .NET Framework. Para saber mais sobre o .NET Framework, veja [versões da estrutura](/dotnet/articles/standard/frameworks).

A biblioteca do .NET Framework dá suporte apenas ao modelo de programação do WCF e conta com um protocolo binário proprietário baseado no transporte WCF `net.tcp`. Este protocolo e biblioteca são mantidos para compatibilidade com aplicativos existentes.

A biblioteca do .NET Standard é baseada na definição de protocolo aberto para a Retransmissão de Conexões Híbridas com base em HTTP e WebSockets. A biblioteca dá suporte a uma abstração de fluxo sobre WebSockets e um gesto de API de solicitação-resposta simples para responder a solicitações HTTP. O exemplo [API da Web](https://github.com/Azure/azure-relay-dotnet) mostra como integrar Conexões Híbridas com ASP.NET Core para serviços Web.

#### <a name="nodejs"></a>Node.js

Os módulos de Conexões Híbridas listados na tabela acima substituem ou corrigem módulos Node.js existentes por implementações alternativas que escutam no serviço de Retransmissão do Azure em vez da pilha de rede local.

O módulo `hyco-https` corrige e substitui parcialmente os módulos Node.js principais `http` e `https`, fornecendo uma implementação de ouvinte HTTPS que é compatível com muitos aplicativos de módulos Node.js existentes que dependem desses módulos principais.

Os módulos `hyco-ws` e `hyco-websocket` corrigem os módulos populares `ws` e `websocket` para Node.js, fornecendo implementações de ouvinte alternativas que permitem que os módulos e aplicativos dependentes de qualquer módulo trabalhem na Retransmissão de Conexões Híbridas.

Detalhes sobre esses módulos podem ser encontrados no repositório GitHub [azure-relay-node](https://github.com/Azure/azure-relay-node).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a Retransmissão do Azure, visite estes links:
* [O que é Retransmissão do Azure?](relay-what-is-it.md)
* [Perguntas frequentes sobre retransmissão](relay-faq.yml)
