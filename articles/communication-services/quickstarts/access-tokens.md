---
title: Início rápido – Criar e gerenciar tokens de acesso
titleSuffix: An Azure Communication Services quickstart
description: Saiba como gerenciar identidades e tokens de acesso usando a biblioteca de clientes da Identidade dos Serviços de Comunicação do Azure.
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 08/20/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 8937210b366981d9768e5329989a8ad14f5e8f35
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657036"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>Início Rápido: Criar e gerenciar tokens de acesso

Comece a usar os Serviços de Comunicação do Azure por meio da biblioteca de clientes da Identidade dos Serviços de Comunicação. Isso permite que você crie identidades e gerencie os tokens de acesso. A identidade representa a entidade do aplicativo no Serviço de Comunicação do Azure (por exemplo, o usuário ou o dispositivo). Os tokens de acesso permitem que suas bibliotecas de clientes de Chat e Chamada se autentiquem diretamente nos Serviços de Comunicação do Azure. É recomendável gerar tokens de acesso em um serviço do lado do servidor. Em seguida, os tokens de acesso são usados para inicializar as bibliotecas de clientes dos Serviços de Comunicação em dispositivos cliente.

Todos os preços vistos nas imagens ao longo deste tutorial são apenas para fins de demonstração.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/user-access-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/user-access-token-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/user-access-token-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/user-access-token-java.md)]
::: zone-end

A saída do aplicativo descreve cada ação concluída:
<!---cSpell:disable --->
```console
Azure Communication Services - Access Tokens Quickstart

Created an identity: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Issued an access token with 'voip' scope that expires at Fri Nov 27 2020 16:47:05 GMT-0800 (Pacific Standard Time):
<token signature here>

Successfully revoked all access tokens for identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Deleted the identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502
```
<!---cSpell:enable --->

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços de Comunicação, exclua o recurso ou o grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele. Saiba mais sobre [como limpar recursos](./create-communication-resource.md#clean-up-resources).


## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

> [!div class="checklist"]
> * Gerenciar identidades
> * Emitir tokens de acesso
> * Usar a biblioteca de clientes da Identidade dos Serviços de Comunicação


> [!div class="nextstepaction"]
> [Adicionar chamada de voz ao aplicativo](./voice-video-calling/getting-started-with-calling.md)

Você também pode querer:

 - [Aprender sobre autenticação](../concepts/authentication.md)
 - [Adicionar o chat ao seu aplicativo](./chat/get-started.md)
 - [Saber mais sobre a arquitetura cliente e servidor](../concepts/client-and-server-architecture.md)
