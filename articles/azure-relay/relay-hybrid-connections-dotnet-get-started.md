---
title: Retransmissão do Azure Conexões Híbridas-WebSockets no .NET
description: Escreva um aplicativo de console em C# para retransmissão do Azure Conexões Híbridas WebSockets.
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 06/23/2020
ms.openlocfilehash: bf22b8b11dc386644803b43ee4e3a51d04b70419
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90527421"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-net"></a>Introdução às WebSockets de Conexões Híbridas de Retransmissão no .NET
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Neste Início Rápido, você cria aplicativos de remetente e destinatário de .NET que enviam e recebem mensagens por meio de WebSockets de Conexões Híbridas na Retransmissão do Azure. Para saber mais sobre a Retransmissão do Azure em geral, confira [Retransmissão do Azure](relay-what-is-it.md). 

Neste início rápido, você segue os seguintes passos:

1. Criar um namespace de Retransmissão usando o Portal do Azure.
2. Crie uma conexão híbrida nesse namespace usando o Portal do Azure.
3. Escreva um aplicativo de console do servidor (ouvinte) para receber mensagens.
4. Escreva um aplicativo de console de cliente (remetente) para enviar mensagens.
5. Execute aplicativos. 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

* [Visual Studio 2015 ou posterior](https://www.visualstudio.com). Os exemplos neste tutorial usam o Visual Studio 2017.
* Uma assinatura do Azure. Se você não tiver [uma conta gratuita](https://azure.microsoft.com/free/), crie uma antes de começar.

## <a name="create-a-namespace"></a>Criar um namespace
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Criar uma Conexão Híbrida
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Criar um aplicativo de servidor (escuta)
No Visual Studio, grave um aplicativo de console em C# para escutar e receber mensagens da retransmissão.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Criar um aplicativo de cliente (remetente)
No Visual Studio, grave um aplicativo de console em C# para enviar mensagens à retransmissão.

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="run-the-applications"></a>Executar os aplicativos
1. Execute o aplicativo de servidor.
2. Execute o aplicativo de cliente e insira algum texto.
3. Verifique se o console do aplicativo para servidores exibe o texto inserido no aplicativo cliente.

    ![Janelas de console testando os aplicativos cliente e servidor.](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

Parabéns, você criou um aplicativo de Conexões Híbridas completo!

## <a name="next-steps"></a>Próximas etapas
Neste Início Rápido, você criou aplicativos de cliente e servidor do .NET que usavam WebSockets para enviar e receber mensagens. O recurso Conexões Híbridas de Retransmissão do Azure também dá suporte ao uso de HTTP para enviar e receber mensagens. Para saber como usar HTTP com as Conexões Híbridas de Retransmissão do Azure, consulte o [Início Rápido do HTTP](relay-hybrid-connections-http-requests-dotnet-get-started.md).

Neste Início Rápido, você usou o .NET Framework para criar aplicativos cliente e servidor. Para saber como escrever aplicativos cliente e servidor usando o Node.js, confira o [Início Rápido de WebSockets do Node.js](relay-hybrid-connections-node-get-started.md) ou o [Início Rápido do HTTP Node.js](relay-hybrid-connections-http-requests-dotnet-get-started.md).

