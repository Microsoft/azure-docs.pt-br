---
title: "Integração do SDK do Windows Universal para o Azure Mobile Engagement | Microsoft Docs"
description: "Integração do SDK do Windows Universal para o Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 9ded187d-5c07-4377-a41c-ce205dd38b50
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2016
ms.author: piyushjo
ms.openlocfilehash: d616ad58156a19e89b3e106639a38df67cbd0abb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="windows-universal-sdk-integration-for-azure-mobile-engagement"></a>Integração do SDK do Windows Universal para o Azure Mobile Engagement
Este documento descreve todas as opções de integração e configuração disponíveis para o SDK do Windows Universal para o Azure Mobile Engagement.

## <a name="prerequisites"></a>Pré-requisitos
Antes de iniciar este tutorial, você deve primeiro concluir nosso [tutorial de 15 minutos](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="advanced-features"></a>Recursos avançados
### <a name="reporting-features"></a>Recursos de relatório
Você pode adicionar esses recursos:

1. [Opções de relatório avançadas](mobile-engagement-windows-store-advanced-reporting.md)
2. [Opções de configuração avançada](mobile-engagement-windows-store-advanced-configuration.md)

### <a name="notifications"></a>Notificações
[Como integrar o Reach (Notificações) em seu aplicativo universal do Windows](mobile-engagement-windows-store-integrate-engagement-reach.md)

### <a name="tag-plan-implementation"></a>Implementação do plano de marca:
[Como usar a API de marcação avançada do Mobile Engagement em seu aplicativo universal do Windows](mobile-engagement-windows-store-use-engagement-api.md)

## <a name="release-notes"></a>Notas de versão
### <a name="341-11032016"></a>3.4.1 (11/03/2016)

* Aprimoramentos de estabilidade.

Para versões anteriores, consulte as [notas de versão completas](mobile-engagement-windows-store-release-notes.md)

## <a name="upgrade-procedures"></a>Procedimentos de atualização
Se você já tiver integrado uma versão anterior do Engagement no seu aplicativo, você deve considerar os seguintes pontos ao atualizar o SDK.

Você precisará seguir vários procedimentos se tiver perdido várias versões do SDK. Ver todos os [Procedimentos de atualização](mobile-engagement-windows-store-upgrade-procedure.md). Por exemplo, se você migrar do 0.10.1 para 0.11.0 você tem que primeiro seguir o procedimento "de 0.9.0 a 0.10.1” e depois o procedimento "de 0.10.1 a 0.11.0".

### <a name="from-330-to-340"></a>De 3.3.0 a 3.4.0
#### <a name="test-logs"></a>Logs de teste
Agora, os logs do console produzidos pelo SDK podem ser habilitados/desabilitados/filtrados. Para personalizar esse recurso, atualize a propriedade `EngagementAgent.Instance.TestLogEnabled` para um dos valores disponíveis na enumeração `EngagementTestLogLevel`, por exemplo:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

#### <a name="resources"></a>Recursos
A sobreposição do Alcance foi aprimorada. Ela faz parte dos recursos do pacote NuGet do SDK.

Ao atualizar para a nova versão do SDK, você pode escolher se deseja ou não manter os arquivos existentes da pasta de sobreposição de seus recursos:

* Se a sobreposição anterior estiver funcionando para você ou se você estiver integrando os elementos `WebView` manualmente, você poderá optar por manter os arquivos existentes e ela ainda funcionará.
* Para atualizar para a nova sobreposição, substitua toda a pasta `overlay` dos seus recursos pela nova pasta do pacote do SDK (aplicativos UWP: após a atualização, você pode obter a nova pasta de sobreposição de %USERPROFILE%\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [!WARNING]
> Usar a nova sobreposição substituirá todas as personalizações feitas na versão anterior.
> 
> 

### <a name="upgrade-from-older-versions"></a>Atualizar de versões anteriores
Consulte [Procedimentos de atualização](mobile-engagement-windows-store-upgrade-procedure.md)

