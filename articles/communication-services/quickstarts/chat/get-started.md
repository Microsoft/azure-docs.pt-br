---
title: Início rápido – Adicionar o chat ao seu aplicativo
titleSuffix: An Azure Communication Services quickstart
description: Este guia de início rápido mostra como adicionar o chat dos Serviços de Comunicação ao seu aplicativo.
author: fanche
manager: phans
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 2d0d80be367dda3689566dec2ade6fd7fc7c01fc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91665354"
---
# <a name="quickstart-add-chat-to-your-app"></a>Início Rápido: Adicionar o chat ao seu aplicativo

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Comece a usar os Serviços de Comunicação do Azure com a biblioteca de clientes do Chat dos Serviços de Comunicação para adicionar chat em tempo real ao seu aplicativo. Neste guia de início rápido, usaremos a biblioteca de clientes do Chat para criar conversas de chat que permitem que os usuários tenham conversas entre si. Para saber mais sobre os conceitos do Chat, acesse a [documentação conceitual do chat](../../concepts/chat/concepts.md).

::: zone pivot="programming-language-javascript"
[!INCLUDE [Chat with JavaScript client library](./includes/chat-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Chat with Python client library](./includes/chat-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Chat with Java client library](./includes/chat-java.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [Chat with C# client library](./includes/chat-csharp.md)]
::: zone-end

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços de Comunicação, exclua o recurso ou o grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele. Saiba mais sobre [como limpar recursos](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

> [!div class="checklist"]
> * Criar um cliente de chat
> * Criar uma conversa com dois usuários
> * Enviar uma mensagem para a conversa
> * Receber mensagens de uma conversa
> * Remover usuários de uma conversa

> [!div class="nextstepaction"]
> [Experimente o aplicativo Chat Hero](../../samples/chat-hero-sample.md)

Você também pode querer:

 - Saber mais sobre os [conceitos de chat](../../concepts/chat/concepts.md)
 - Familiarizar-se com a [biblioteca de clientes do chat](../../concepts/chat/sdk-features.md)
