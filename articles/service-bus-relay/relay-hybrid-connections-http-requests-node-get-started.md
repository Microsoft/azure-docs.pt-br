---
title: Introdução às Conexões Híbridas de Retransmissão do Azure de pedidos HTTP no Nó | Microsoft Docs
description: Escreva um aplicativo de console Node.js para Conexões Híbridas da Retransmissão do Azure de pedidos HTTP em Nó.
services: service-bus-relay
documentationcenter: node
author: clemensv
manager: timlt
editor: ''
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 11/01/2018
ms.author: clemensv
ms.openlocfilehash: e54a096bd27efddaa9eafb8619e787178550a6e0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553923"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-node"></a>Introdução às Conexões Híbridas de Retransmissão de pedidos HTTP no Nó

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Neste Início Rápido, você cria aplicativos Node.js remetente e destinatário que enviam e recebem mensagens usando o protocolo HTTP. Os aplicativos usam o recurso Conexões Híbridas da Retransmissão do Azure. Para saber mais sobre a Retransmissão do Azure em geral, confira [Retransmissão do Azure](relay-what-is-it.md). 

Neste início rápido, você segue os seguintes passos:

1. Criar um namespace de Retransmissão usando o Portal do Azure.
2. Crie uma conexão híbrida nesse namespace usando o Portal do Azure.
3. Escreva um aplicativo de console do servidor (ouvinte) para receber mensagens.
4. Escreva um aplicativo de console de cliente (remetente) para enviar mensagens.
5. Execute aplicativos.

## <a name="prerequisites"></a>Pré-requisitos
- [Node.js](https://nodejs.org/en/).
- Uma assinatura do Azure. Se você não tiver [uma conta gratuita](https://azure.microsoft.com/free/), crie uma antes de começar.

## <a name="create-a-namespace-using-the-azure-portal"></a>Criar um namespace usando o portal do Azure
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection-using-the-azure-portal"></a>Criar uma conexão híbrida usando o portal do Azure
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>Criar um aplicativo de servidor (escuta)
Para escutar e receber mensagens da retransmissão, grave um aplicativo de console Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-http-requests-node-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Criar um aplicativo de cliente (remetente)

Para enviar mensagens à Retransmissão, você pode usar qualquer cliente HTTP ou gravar um aplicativo de console Node.js.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-http-requests-node-get-started-client.md)]

## <a name="run-the-applications"></a>Executar os aplicativos

1. Execute o aplicativo de servidor: de um tipo de prompt de comando `node listener.js` do Node.js.
2. Execute o aplicativo cliente: de um prompt de comando do Node.js, digite `node sender.js` e insira um texto.
3. Certifique-se de que o console do aplicativo de servidor exiba o texto inserido no aplicativo de cliente.

Parabéns, você criou um aplicativo de Conexões Híbridas de ponta a ponta usando o Node.js!

## <a name="next-steps"></a>Próximas etapas
Neste Início Rápido, você criou aplicativos de cliente e de servidor do Node.js que usavam HTTP para enviar e receber mensagens. O recurso Conexões Híbridas da Retransmissão do Azure também dá suporte ao uso de WebSockets para enviar e receber mensagens. Para saber como usar o WebSockets com as Conexões Híbridas da Retransmissão do Azure, confira o [Início Rápido do WebSockets](relay-hybrid-connections-node-get-started.md).

Neste Início Rápido, você usou o Node.js para criar aplicativos cliente e servidor. Para saber como escrever aplicativos cliente e servidor usando o .NET Framework, confira o [Início Rápido do WebSockets .NET](relay-hybrid-connections-dotnet-get-started.md) ou o [Início Rápido do HTTP .NET](relay-hybrid-connections-http-requests-dotnet-get-started.md).
