---
title: "Integração do SDK do Android para Azure Mobile Engagement"
description: Descreve como integrar o SDK do Azure Mobile Engagement em aplicativos Android
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a91ed04f-f3ce-4692-a6dd-b56a28d7dee8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo;ricksal
ms.openlocfilehash: 35935e911f1f17989beb71978396c6d1b7d601d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="android-sdk-integration-for-azure-mobile-engagement"></a>Integração do Android SDK para Azure Mobile Engagement
> [!div class="op_single_selector"]
> * [Universal do Windows](mobile-engagement-windows-store-sdk-overview.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-sdk-overview.md)
> * [iOS](mobile-engagement-ios-sdk-overview.md)
> * [Android](mobile-engagement-android-sdk-overview.md)
> 
> 

Este documento descreve todas as opções de integração e configuração disponíveis para o SDK do Azure Mobile Engagement para Android.

## <a name="prerequisites"></a>Pré-requisitos
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="advanced-features"></a>Recursos avançados
### <a name="reporting-features"></a>Recursos de relatório
Você pode adicionar esses recursos:

1. [Opções de relatório avançadas](mobile-engagement-android-advanced-reporting.md)
2. [Opções de relatório de localização](mobile-engagement-android-location-reporting.md)
3. [Opções de configuração avançada](mobile-engagement-android-advanced-configuration.md)

### <a name="notifications"></a>Notificações:
[Como integrar o Reach (notificações) em seu aplicativo Android](mobile-engagement-android-integrate-engagement-reach.md)

1. GCM (Google Cloud Messaging): [como integrar o GCM com o Mobile Engagement](mobile-engagement-android-gcm-integrate.md)
2. ADM (Amazon Device Messaging): [como integrar o ADM com o Mobile Engagement](mobile-engagement-android-adm-integrate.md)

### <a name="tag-plan-implementation"></a>Implementação do plano de marca:
[Como usar a API de marcação avançada do Mobile Engagement em seu aplicativo Android](mobile-engagement-android-use-engagement-api.md)

## <a name="release-notes"></a>Notas de versão

### <a name="431-07172017"></a>4.3.1 (07/17/2017)
* Corrigir uma falha que raramente poderia acontecer ao chamar `EngagementAgentUtils.isInDedicatedEngagementProcess`, que também é usado pela classe `EngagementApplication`.

### <a name="430-06272017"></a>4.3.0 (06/27/2017)
* Suporte para Android 8 (versões anteriores do SDK não funcionarão em Android 8).
* Não há mais dependência da biblioteca de suporte.
* Remover classe `EngagementFragmentActivity`.
* Devido a [Limites de Execução em Segundo de Fundo](https://developer.android.com/preview/features/background.html) no Android 8, logs no segundo plano podem ser atrasados até que o usuário interaja com o dispositivo. Isso terá um impacto sobre Campanha de Push **Entregue** e estatísticas de **Notificação do sistema exibida** sendo atrasadas no caso de dispositivo estar em suspensão (a notificação ainda será exibida, tocará e vibrará em tempo real sem problemas).
* Devido a [Limites de Local do Segundo Plano](https://developer.android.com/preview/features/background-location-limits.html), o local em tempo real em segundo plano não será atualizado com frequência no Android 8.

Para todas as versões, consulte as [notas de versão completas](mobile-engagement-android-release-notes.md).

## <a name="upgrade-procedures"></a>Procedimentos de atualização
Se você já tiver integrado uma versão mais antiga do nosso SDK em seu aplicativo, consulte os [Procedimentos de atualização](mobile-engagement-android-upgrade-procedure.md).

