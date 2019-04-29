---
title: Introdução às Conexões Híbridas de Retransmissão do Azure de pedidos HTTP no .NET | Microsoft Docs
description: Escreva um aplicativo de console C# para Conexões Híbridas da Retransmissão do Azure de pedidos HTTP em .NET.
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: 5bb45ff87f558e1142b68af01147ad55386ee28f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60749490"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-net"></a>Introdução às Conexões Híbridas de Retransmissão de pedidos HTTP no .NET
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

Neste Início Rápido, você cria aplicativos .NET remetente e destinatário que enviam e recebem mensagens usando o protocolo HTTP. Os aplicativos usam o recurso Conexões Híbridas da Retransmissão do Azure. Para saber mais sobre a Retransmissão do Azure em geral, confira [Retransmissão do Azure](relay-what-is-it.md). 

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

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-server](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>Criar um aplicativo de cliente (remetente)
No Visual Studio, grave um aplicativo de console em C# para enviar mensagens à retransmissão.

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-client](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-client.md)]

## <a name="run-the-applications"></a>Executar os aplicativos
1. Execute o aplicativo de servidor. Você vê o texto a seguir na janela do console:

    ```
    Online
    Server listening
    ```
1. Execute o aplicativo cliente. Você verá `hello!` na janela do cliente. O cliente enviou uma solicitação HTTP para o servidor e o servidor respondeu com um `hello!`. 
3. Agora, para fechar as janelas do console, pressione **ENTER** nas duas janelas do console. 

Parabéns, você criou um aplicativo de Conexões Híbridas de ponta a ponta!

## <a name="next-steps"></a>Próximas etapas

Neste Início Rápido, você criou aplicativos de cliente e servidor do .NET que usavam HTTP para enviar e receber mensagens. O recurso Conexões Híbridas da Retransmissão do Azure também dá suporte ao uso de WebSockets para enviar e receber mensagens. Para saber como usar o WebSockets com as Conexões Híbridas da Retransmissão do Azure, confira o [Início Rápido do WebSockets](relay-hybrid-connections-dotnet-get-started.md).

Neste Início Rápido, você usou o .NET Framework para criar aplicativos cliente e servidor. Para saber como escrever aplicativos cliente e servidor usando o Node.js, confira o [Início Rápido de WebSockets do Node.js](relay-hybrid-connections-node-get-started.md) ou o [Início Rápido do HTTP Node.js](relay-hybrid-connections-http-requests-dotnet-get-started.md).