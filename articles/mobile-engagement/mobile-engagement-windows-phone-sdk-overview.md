---
title: "Visão geral do SDK do Windows Phone Silverlight no Azure Mobile Engagement | Microsoft Docs"
description: "Visão geral do SDK do Windows Phone Silverlight para o Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 0e3d2420-0509-4952-8891-392e3dad9aaf
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2016
ms.author: piyushjo
ms.openlocfilehash: c4e8ceee4104c3d3a6c3e6b79322ba1cf8463b22
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="windows-phone-silverlight-sdk-overview-for-azure-mobile-engagement"></a>Visão geral do SDK do Windows Phone Silverlight para o Azure Mobile Engagement
Comece aqui para obter todos os detalhes sobre como integrar o Azure Mobile Engagement em um aplicativo do Windows Phone Silverlight. Se você gostaria de experimentá-lo primeiro, faça nosso [tutorial de 15 minutos](mobile-engagement-windows-phone-get-started.md).

Clique para ver o [Conteúdo do SDK](mobile-engagement-windows-phone-sdk-content.md)

## <a name="integration-procedures"></a>Procedimentos de integração
1. Comece aqui: [Como integrar o Mobile Engagement em seu aplicativo do Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
2. Para Notificações: [Como integrar o Reach (Notificações) em seu aplicativo do Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement-reach.md)
3. Implementação do plano de marcação: [Como usar a API de marcação avançada do Mobile Engagement em seu aplicativo do Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md)

## <a name="release-notes"></a>Notas de versão
###<a name="331-11032016"></a>3.3.1 (11/03/2016)
Parte do pacote Nuget *MicrosoftAzure.MobileEngagement* **v3.4.1**

* Aprimoramentos de estabilidade.

Para a versão anterior, consulte as [notas de versão completas](mobile-engagement-windows-phone-release-notes.md)

## <a name="upgrade-procedures"></a>Procedimentos de atualização
Se você já tiver integrado uma versão anterior do SDK no seu aplicativo, você deve considerar os seguintes pontos ao atualizar o SDK.

Você precisará seguir vários procedimentos se perdeu várias versões do SDK. Ver todos os [Procedimentos de atualização](mobile-engagement-windows-phone-upgrade-procedure.md). Por exemplo, se você migrar do 0.10.1 para 0.11.0 você tem que primeiro seguir o procedimento "de 0.9.0 a 0.10.1” e depois o procedimento "de 0.10.1 a 0.11.0".

### <a name="from-200-to-330"></a>De 2.0.0 a 3.3.0
#### <a name="test-logs"></a>Logs de teste
Agora, os logs do console produzidos pelo SDK podem ser habilitados/desabilitados/filtrados. Para personalizar esse recurso, atualize a propriedade `EngagementAgent.Instance.TestLogEnabled` para um dos valores disponíveis na enumeração `EngagementTestLogLevel`, por exemplo:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="upgrade-from-older-versions"></a>Atualizar de versões anteriores
Consulte [Procedimentos de atualização](mobile-engagement-windows-phone-upgrade-procedure.md)

