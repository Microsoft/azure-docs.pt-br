---
title: Início Rápido – Interoperabilidade do Teams nos Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services quickstart
description: Neste guia de início rápido, você aprenderá a participar de uma reunião do Teams com o SDK de Chamada de Comunicação do Azure.
author: matthewrobertson
ms.author: chpalm
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-web-ios-android
ms.openlocfilehash: f8daed736f1d7f5ecf77a5b44f95d191eb5930f8
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120446"
---
# <a name="quickstart-join-your-calling-app-to-a-teams-meeting"></a>Início Rápido: Ingressar seu aplicativo de chamada em uma reunião do Teams

> [!IMPORTANT]
> Para habilitar/desabilitar a [interoperabilidade de locatários do Teams](../../concepts/teams-interop.md), preencha [este formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u).

Comece a usar os Serviços de Comunicação do Azure conectando sua solução de chamada ao Microsoft Teams usando a biblioteca de clientes JavaScript.

::: zone pivot="platform-web"
[!INCLUDE [Calling with JavaScript](./includes/teams-interop-javascript.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Calling with Android](./includes/teams-interop-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Calling with iOS](./includes/teams-interop-ios.md)]
::: zone-end

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços de Comunicação, exclua o recurso ou o grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele. Saiba mais sobre [como limpar recursos](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos:

- Confira nosso [exemplo de destaque da chamada](../../samples/calling-hero-sample.md)
- Saiba mais sobre as [funcionalidades da biblioteca de clientes de chamada](./calling-client-samples.md)
- Saiba mais sobre [como a chamada funciona](../../concepts/voice-video-calling/about-call-types.md)
