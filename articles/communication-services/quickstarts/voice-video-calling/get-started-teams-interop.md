---
title: Início Rápido – Interoperabilidade do Teams nos Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services quickstart
description: Neste guia de início rápido, você aprenderá a participar de uma reunião do Teams com o SDK de Chamada de Comunicação do Azure.
author: chpalm
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-web-ios-android
ms.openlocfilehash: 0e75d2b480a9cbfd2977d9d449c1ea12bdfe4920
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106095604"
---
# <a name="quickstart-join-your-calling-app-to-a-teams-meeting"></a>Início Rápido: Ingressar seu aplicativo de chamada em uma reunião do Teams

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

> [!IMPORTANT]
> Para habilitar/desabilitar a [interoperabilidade de locatários do Teams](../../concepts/teams-interop.md), preencha [este formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u).

Comece a usar os Serviços de Comunicação do Azure conectando sua solução de chamada ao Microsoft Teams usando o SDK de JavaScript.

::: zone pivot="platform-web"
[!INCLUDE [Calling with JavaScript](./includes/teams-interop-javascript.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Calling with Android](./includes/teams-interop-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Calling with iOS](./includes/teams-interop-ios.md)]
::: zone-end

A funcionalidade descrita neste documento usa a versão de disponibilidade geral dos SDKs dos Serviços de Comunicação. A Interoperabilidade do Teams exige a versão beta dos SDKs dos Serviços de Comunicação. Os SDKs beta podem ser explorados na [página de notas sobre a versão](https://github.com/Azure/Communication/tree/master/releasenotes).

Ao executar a etapa "Instalar pacote" com os SDKs beta, modifique a versão do pacote para a última versão beta especificando a versão `@1.0.0-beta.10` (versão no momento da escrita deste artigo) no nome do pacote `communication-calling`. Você não precisa modificar o comando de pacote `communication-common`. Por exemplo:

```console
npm install @azure/communication-calling@1.0.0-beta.10 --save
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços de Comunicação, exclua o recurso ou o grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele. Saiba mais sobre [como limpar recursos](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos:

- Confira nosso [exemplo de destaque da chamada](../../samples/calling-hero-sample.md)
- Conheça os recursos do [SDK de Chamada](./calling-client-samples.md)
- Saiba mais sobre [como a chamada funciona](../../concepts/voice-video-calling/about-call-types.md)
