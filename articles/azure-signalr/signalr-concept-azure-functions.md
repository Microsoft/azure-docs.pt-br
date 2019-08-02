---
title: Crie aplicativos em tempo real com o Azure Functions e o serviço Azure SignalR
description: Uma visão geral de como usar o serviço do Azure SignalR em aplicativos sem servidor.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: c88e2b012086f61aad3143c8fe6b0bd25ec333e5
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275102"
---
# <a name="build-real-time-apps-with-azure-functions-and-azure-signalr-service"></a>Crie aplicativos em tempo real com o Azure Functions e o serviço do Azure SignalR

Como o Azure SignalR e o Azure Functions Service são serviços totalmente gerenciados e altamente escalonáveis que permitem que você se concentre na criação de aplicativos e não no gerenciamento da infraestrutura, é comum usar os dois serviços juntos para fornecer comunicação em tempo real em um ambiente [sem servidor](https://azure.microsoft.com/solutions/serverless/).

> [!NOTE]
> Saiba como usar o SignalR e Azure Functions juntos no tutorial interativo [habilitar as atualizações automáticas em um aplicativo web usando o Azure Functions e o SignalR Service](https://docs.microsoft.com/learn/modules/automatic-update-of-a-webapp-using-azure-functions-and-signalr).

## <a name="integrate-real-time-communications-with-azure-services"></a>Integrar a comunicação em tempo real com os serviços do Azure

O Azure Functions permite que você escreva códigos em [várias linguagens](../azure-functions/supported-languages.md), incluindo JavaScript, C# e Java, que são disparados sempre que ocorrem eventos na nuvem. Exemplos desses eventos incluem:

* Solicitações HTTP e webhook
* Temporizadores periódicos
* Eventos de serviços do Azure, como:
    - Grade de Eventos
    - Hubs de Eventos
    - Barramento de Serviço
    - Feed de alterações do Cosmos DB
    - Armazenamento – blobs e filas
    - Conectores de Aplicativos Lógicos, como Salesforce e SQL Server

Usando o Azure Functions para integrar esses eventos ao Azure SignalR Service, você tem a capacidade de notificar milhares de clientes, sempre que ocorrem eventos.

Alguns cenários comuns para mensagens sem servidor em tempo real que você pode implementar com o Azure Functions e o SignalR Service incluem:

* Visualizar a telemetria do dispositivo IoT em um painel ou mapa em tempo real
* Atualizar dados em um aplicativo quando os documentos são atualizados no Cosmos DB
* Enviar notificações no aplicativo quando novos pedidos são criados no Salesforce

## <a name="signalr-service-bindings-for-azure-functions"></a>Associações do Serviço SignalR para o Azure Functions

As associações do SignalR Service ao Azure Functions permitem que um aplicativo de funções do Azure publique mensagens em clientes conectados ao SignalR Service. Os clientes podem se conectar ao serviço usando um SDK cliente do SignalR que está disponível em .NET, JavaScript e Java, com mais linguagens em breve.

### <a name="an-example-scenario"></a>Um cenário de exemplo

Um exemplo de como usar as associações do SignalR Service é usar o Azure Functions para integrar ao Azure Cosmos DB e ao SignalR Service para enviar mensagens em tempo real quando novos eventos são exibidos em um feed de alterações do Cosmos DB.

![Cosmos DB, Azure Functions, SignalR Service](media/signalr-concept-azure-functions/signalr-cosmosdb-functions.png)

1. Uma alteração é feita em uma coleção do Cosmos DB
2. O evento de alteração é propagado para o feed de alterações do Cosmos DB
3. Um Azure Functions é disparado pelo evento de alteração usando o gatilho do Cosmos DB
4. A associação de saída do SignalR Service publica uma mensagem no SignalR Service
5. O SignalR Service publica a mensagem a todos os clientes conectados

### <a name="authentication-and-users"></a>Autenticação e os usuários

O SignalR Service permite difundir mensagens para todos os clientes ou apenas a um subconjunto de clientes, como aqueles que pertencem a um único usuário. As associações do SignalR Service ao Azure Functions podem ser combinadas com a autenticação do Serviço de Aplicativo para autenticar usuários em provedores, como Azure Active Directory, Facebook e Twitter. Em seguida, você pode enviar mensagens diretamente para esses usuários autenticados.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você obteve uma visão geral de como usar o Azure Functions com SignalR Service para habilitar uma ampla variedade de cenários de mensagens em tempo real sem servidor.

Para obter detalhes completos sobre como usar o Azure Functions e o SignalR Service juntos, visite os seguintes recursos:

* [Configuração com SignalR Service e desenvolvimento de funções do Azure](signalr-concept-serverless-development-config.md)
* [Habilitar as atualizações automáticas em um aplicativo web usando o Azure Functions e o SignalR Service](https://docs.microsoft.com/learn/modules/automatic-update-of-a-webapp-using-azure-functions-and-signalr)

Siga um destes inícios rápidos para saber mais.

* [Início Rápido do Azure SignalR Service sem servidor – C#](signalr-quickstart-azure-functions-csharp.md)
* [Início Rápido do Azure SignalR Service sem servidor – JavaScript](signalr-quickstart-azure-functions-javascript.md)