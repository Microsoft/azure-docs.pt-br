---
title: Início rápido – Criar e gerenciar tokens de acesso
titleSuffix: An Azure Communication Services quickstart
description: Saiba como gerenciar identidades e tokens de acesso usando o SDK da Identidade dos Serviços de Comunicação do Azure.
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: e356219d22ee558ce3de5a96d58f24b9e7902d8a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726610"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>Início Rápido: Criar e gerenciar tokens de acesso

Comece a usar os Serviços de Comunicação do Azure com o SDK de Identidade dos Serviços de Comunicação. Isso permite que você crie identidades e gerencie os tokens de acesso. A identidade representa a entidade do aplicativo no Serviço de Comunicação do Azure (por exemplo, o usuário ou o dispositivo). Os tokens de acesso permitem seus SDKs de Chat e de Chamada se autentiquem diretamente nos Serviços de Comunicação do Azure. É recomendável gerar tokens de acesso em um serviço do lado do servidor. Em seguida, os tokens de acesso são usados para inicializar os SDKs dos Serviços de Comunicação em dispositivos cliente.

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

Created an identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-19e0-2727-80f5-8b3a0d003502

Issued an access token with 'voip' scope that expires at 30/03/21 08:09 09 AM:
<token signature here>

Created an identity with ID: 8:acs:4ccc92c8-9815-4422-bddc-ceea181dc774_00000006-1ce9-31b4-54b7-a43a0d006a52

Issued an access token with 'voip' scope that expires at 30/03/21 08:09 09 AM:
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
> * Usar os SDK de Identidade dos Serviços de Comunicação


> [!div class="nextstepaction"]
> [Adicionar chamada de voz ao aplicativo](./voice-video-calling/getting-started-with-calling.md)

Você também pode querer:

 - [Aprender sobre autenticação](../concepts/authentication.md)
 - [Adicionar o chat ao seu aplicativo](./chat/get-started.md)
 - [Saber mais sobre a arquitetura cliente e servidor](../concepts/client-and-server-architecture.md)
