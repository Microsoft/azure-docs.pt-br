---
title: "Introdução às conexões híbridas de retransmissão do Azure no Nó | Microsoft Docs"
description: "Escreva um aplicativo de console Node.js para as Conexões Híbridas de Retransmissão do Azure."
services: service-bus-relay
documentationcenter: node
author: sethmanheim
manager: timlt
editor: 
ms.assetid: e44e4867-3cf3-46be-8f8a-7671e2013bc4
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: node
ms.workload: na
ms.date: 07/07/2017
ms.author: sethm
ms.openlocfilehash: c3bfc45969f250059988129f532edd12dfe3dcfe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-relay-hybrid-connections"></a>Introdução às Conexões Híbridas de Retransmissão

[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Este tutorial fornece uma introdução ao [Conexões Híbridas de Retransmissão do Azure](relay-what-is-it.md#hybrid-connections)e mostra como usar o Node.js para criar um aplicativo cliente que envia mensagens para um aplicativo de escuta correspondente. 

## <a name="what-will-be-accomplished"></a>O que será realizado

Como as Conexões Híbridas exigem um componente de cliente e de servidor, criaremos dois aplicativos de console neste tutorial. Siga estas etapas:

1. Criar um namespace de retransmissão usando o Portal do Azure.
2. Criar uma conexão híbrida usando o portal do Azure.
3. Escrever um aplicativo de console do servidor para enviar mensagens.
4. Escrever um aplicativo de console de cliente para enviar mensagens.

## <a name="prerequisites"></a>Pré-requisitos

1. [Node.js](https://nodejs.org/en/).
2. Uma assinatura do Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Criar um namespace usando o portal do Azure

Se você já tiver um namespace de retransmissão criado, vá até a seção [Criar uma conexão híbrida usando o portal do Azure](#2-create-a-hybrid-connection-using-the-azure-portal).

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. Criar uma conexão híbrida usando o portal do Azure

Se você já tiver uma conexão híbrida criada, vá até a seção [Criar um aplicativo de servidor](#3-create-a-server-application-listener).

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. Criar um aplicativo de servidor (escuta)

Para escutar e receber mensagens da retransmissão, escreveremos um aplicativo de console em Node.

[!INCLUDE [relay-hybrid-connections-node-get-started-server](../../includes/relay-hybrid-connections-node-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. Criar um aplicativo de cliente (remetente)

Para enviar mensagens à retransmissão, escreveremos um aplicativo de console em Node.

[!INCLUDE [relay-hybrid-connections-node-get-started-client](../../includes/relay-hybrid-connections-node-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. Executar os aplicativos

1. Execute o aplicativo de servidor: de um tipo de prompt de comando `node listener.js` do Node.js.
2. Execute o aplicativo cliente: de um prompt de comando do Node.js, digite `node sender.js` e insira um texto.
3. Certifique-se de que o console do aplicativo de servidor exiba o texto inserido no aplicativo de cliente.

![running-applications](./media/relay-hybrid-connections-node-get-started/running-applications.png)

Parabéns, você criou um aplicativo de Conexões Híbridas de ponta a ponta usando o Node.js!

## <a name="next-steps"></a>Próximas etapas:

* [Perguntas frequentes sobre retransmissão](relay-faq.md)
* [Criar um namespace](relay-create-namespace-portal.md)
* [Introdução ao .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introdução ao Node](relay-hybrid-connections-node-get-started.md)

