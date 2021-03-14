---
title: Exibir métricas com Azure Monitor
description: Este artigo mostra como monitorar as métricas com os gráficos de portal do Azure e CLI do Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: ab89c222648a66ad7451f9bb47e254c55b925630
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590750"
---
# <a name="monitor-media-services-metrics"></a>Monitorar as métricas dos Serviços de Mídia do Microsoft Azure

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[Azure monitor](../../azure-monitor/overview.md) permite que você monitore as métricas e os logs de diagnóstico que ajudam a entender como seus aplicativos estão sendo executados. Para obter uma descrição detalhada desse recurso e entender por que você deve usar as métricas dos serviços de mídia do Azure e os logs de diagnóstico, consulte [monitorar as métricas dos serviços de mídia e os logs de diagnóstico](media-services-metrics-diagnostic-logs.md).

O Azure Monitor fornece várias maneiras de interagir com as métricas, incluindo o gráfico delas no portal, acessá-las por meio da API REST ou consultá-las usando CLI do Azure. Este artigo mostra como monitorar as métricas com os gráficos de portal do Azure e CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- [Criar uma conta dos Serviços de Mídia](./create-account-howto.md)
- Examinar  [os logs de diagnóstico e as métricas dos serviços de mídia](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>Exibir métricas no portal do Azure

1. Entre no Portal do Azure em https://portal.azure.com.
1. Navegue até sua conta dos serviços de mídia do Azure e selecione **métricas**.
1. Clique na caixa **escopo** e selecione o recurso que você deseja monitorar.

    A janela **selecionar um escopo** é exibida à direita com a lista de recursos disponíveis para você. Nesse caso, você verá:

    * &lt;Nome da conta dos serviços de mídia&gt;
    * &lt;Nome da conta de serviços de mídia do &gt; / &lt; ponto de extremidade de streaming&gt;
    * &lt;nome da conta de armazenamento&gt;

    Filtre, selecione o recurso e pressione **aplicar**. Para obter detalhes sobre os recursos e as métricas com suporte, consulte [monitorar métricas dos serviços de mídia](media-services-metrics-diagnostic-logs.md).

    > [!NOTE]
    > Para alternar entre os recursos que você deseja monitorar, clique na caixa **origem** novamente e repita esta etapa.

1. Opcional: dê um nome ao seu gráfico (edite o nome pressionando o lápis na parte superior).
1. Adicione as métricas que você deseja exibir.
1. Você pode fixar o gráfico em seu painel.

## <a name="view-metrics-with-azure-cli"></a>Exibir métricas com CLI do Azure

Para obter métricas de "egresso" com CLI do Azure, você executaria o seguinte `az monitor metrics` comando:

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Para obter outras métricas, substitua "egresso" pelo nome da métrica em que você está interessado.

## <a name="see-also"></a>Confira também

- [Métricas do Azure Monitor](../../azure-monitor/data-platform.md)
- [Crie, exiba e gerencie alertas de métrica usando Azure monitor](../../azure-monitor/alerts/alerts-metric.md).

## <a name="next-steps"></a>Próximas etapas

[Logs de diagnóstico](media-services-diagnostic-logs-howto.md)
