---
title: Habilitar o depurador de instantâneos para aplicativos .NET no serviço de aplicativo do Azure | Microsoft Docs
description: Habilitar o depurador de instantâneos para aplicativos .NET no serviço de aplicativo do Azure
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: brahmnes
ms.openlocfilehash: 7ff93dffe2dd82bdbba204b4235a297b337438f5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60784040"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Habilitar o depurador de instantâneos para aplicativos .NET no serviço de aplicativo do Azure

Depurador de instantâneo atualmente funciona para aplicativos ASP.NET e ASP.NET Core em execução nos planos de serviço do Windows no serviço de aplicativo do Azure.

## <a id="installation"></a> Habilitar o depurador de instantâneo
Para habilitar o depurador de instantâneo para um aplicativo, siga as instruções abaixo. Se você estiver executando um tipo diferente de serviço do Azure, aqui estão as instruções para habilitar o depurador de instantâneo em outras plataformas com suporte:
* [Serviços de Nuvem do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Serviços do Service Fabric do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas Virtuais do Azure e Conjuntos de Dimensionamento de Máquinas Virtuais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais ou físicas locais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Depurador de instantâneo do Application Insights é instalado previamente como parte do tempo de execução dos serviços de aplicativo, mas você precisa para ligá-lo e obter instantâneos para seu aplicativo de serviço de aplicativo. Depois de implantar um aplicativo, mesmo se você tiver incluído o SDK do Application Insights no código-fonte, siga as etapas abaixo para permitir que o depurador de instantâneo.

1. Vá ao painel **Serviços de Aplicativos** no portal do Azure.
2. Navegue até o painel **Configurações > Application Insights**.

   ![Habilitar Insights de Aplicativo no portal de Serviços de Aplicativos](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Siga as instruções no painel para criar um novo recurso ou selecione um recurso existente do App Insights para monitorar seu aplicativo. Além disso, certifique-se de ambas as opções de depurador de instantâneo estão **em**.

   ![Adicione a extensão de site do App Insights][Enablement UI]

4. Depurador de instantâneo agora está habilitado para usar uma configuração de aplicativo de serviços de aplicativo.

    ![Configuração de aplicativo para o depurador de instantâneos][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Desabilitar o depurador de instantâneo

Siga as mesmas etapas para **habilitar depurador de instantâneo**, mas mudar de ambas as opções para depurador de instantâneo **Off**.
É recomendável que você tenha habilitado em todos os seus aplicativos para facilitar o diagnóstico de exceções de aplicativo do depurador de instantâneo.

## <a name="next-steps"></a>Próximas etapas

* [Trabalhar com o Application Insights no Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

