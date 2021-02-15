---
title: Retransmissão do Azure Conexões Híbridas-WebSockets no nó
description: Escreva um aplicativo de console Node.js para as Websockets de Conexões Híbridas de Retransmissão do Azure
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: b362caa6570d4a8e212ff7adf4310a0c63e8b755
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91263701"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-nodejs"></a>Introdução às WebSockets de Conexões Híbridas de Retransmissão no Node.js

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Neste início rápido, você cria aplicativos de remetente e destinatário de Node.js que enviam e recebem mensagens por meio de WebSockets de Conexões Híbridas na Retransmissão do Azure. Para saber mais sobre a Retransmissão do Azure em geral, confira [Retransmissão do Azure](relay-what-is-it.md). 

Neste início rápido, você segue os seguintes passos: 

1. Criar um namespace de Retransmissão usando o Portal do Azure.
2. Crie uma conexão híbrida nesse namespace usando o Portal do Azure.
3. Escreva um aplicativo de console do servidor (ouvinte) para receber mensagens.
4. Escreva um aplicativo de console de cliente (remetente) para enviar mensagens.
5. Execute aplicativos. 

## <a name="prerequisites"></a>Pré-requisitos

- [Node.js](https://nodejs.org/en/).
- Uma assinatura do Azure. Se você não tiver [uma conta gratuita](https://azure.microsoft.com/free/), crie uma antes de começar.

## <a name="create-a-namespace"></a>Criar um namespace
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Criar uma Conexão Híbrida
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Criar um aplicativo de servidor (escuta)
Para escutar e receber mensagens da retransmissão, grave um aplicativo de console Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Criar um aplicativo de cliente (remetente)
Para enviar mensagens à Retransmissão, grave um aplicativo de console Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

## <a name="run-the-applications"></a>Executar os aplicativos

1. Execute o aplicativo de servidor: de um tipo de prompt de comando `node listener.js` do Node.js.
2. Execute o aplicativo cliente: de um prompt de comando do Node.js, digite `node sender.js` e insira um texto.
3. Certifique-se de que o console do aplicativo de servidor exiba o texto inserido no aplicativo de cliente.

    ![Janelas de console testando os aplicativos cliente e servidor.](./media/relay-hybrid-connections-node-get-started/running-applications.png)

Parabéns, você criou um aplicativo de Conexões Híbridas de ponta a ponta usando o Node.js!

## <a name="next-steps"></a>Próximas etapas
Neste início rápido, você criou aplicativos de cliente e servidor do Node.js que usavam WebSockets para enviar e receber mensagens. O recurso Conexões Híbridas de Retransmissão do Azure também dá suporte ao uso de HTTP para enviar e receber mensagens. Para saber como usar HTTP com as Conexões Híbridas de Retransmissão do Azure, consulte o [Início rápido do HTTP Node.js](relay-hybrid-connections-http-requests-node-get-started.md).

Neste Início Rápido, você usou o Node.js para criar aplicativos cliente e servidor. Para saber como escrever aplicativos cliente e servidor usando o .NET Framework, confira o [Início Rápido do WebSockets .NET](relay-hybrid-connections-dotnet-get-started.md) ou o [Início Rápido do HTTP .NET](relay-hybrid-connections-http-requests-dotnet-get-started.md).


