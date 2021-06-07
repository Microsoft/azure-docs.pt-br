---
title: Introdução à interoperabilidade do Teams nos Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services quickstart
description: Neste guia de início rápido, você aprenderá a participar de uma reunião do Teams com o SDK de Chat de Comunicação do Azure
author: askaur
ms.author: askaur
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: ba3a589c5d0f09f24950bd3fee8edc7f4dcd4601
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169055"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>Início Rápido: Ingressar seu aplicativo de chat em uma reunião do Teams

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-chat.md)]

> [!IMPORTANT]
> Para habilitar/desabilitar a [interoperabilidade de locatários do Teams](../../concepts/teams-interop.md), preencha [este formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u).

Comece a usar os Serviços de Comunicação do Azure conectando sua solução de chat ao Microsoft Teams usando o SDK de JavaScript. 

## <a name="prerequisites"></a>Pré-requisitos 

1. Uma  [implantação do Teams](/deployoffice/teams-install). 
2. Um [aplicativo de chat](./get-started.md) funcional. 

## <a name="enable-teams-interoperability"></a>Habilitar a interoperabilidade do Teams 

Um usuário dos Serviços de Comunicação que participa de uma reunião do Teams como um usuário convidado pode acessar o chat da reunião somente quando ele se conectou à chamada de reunião do Teams. Confira a documentação sobre a [interoperabilidade do Teams](../voice-video-calling/get-started-teams-interop.md) para saber como adicionar um usuário dos Serviços de Comunicação a uma chamada de reunião do Teams.

Você precisa ser membro da organização proprietária de ambas as entidades para usar esse recurso.

[!INCLUDE [Join Teams meetings](./includes/meeting-interop-javascript.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços de Comunicação, exclua o recurso ou o grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele. Saiba mais sobre [como limpar recursos](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos:

- Confira nosso [exemplo de chat em destaque](../../samples/chat-hero-sample.md)
- Saiba mais sobre [como o chat funciona](../../concepts/chat/concepts.md)