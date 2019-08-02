---
title: Exibir métricas com o Azure Monitor
description: Este artigo mostra como monitorar as métricas com os gráficos de portal do Azure e a CLI do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: d331dc4eb0c6668426e1ab1a01a1dd1dcebe0c85
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795813"
---
# <a name="monitor-media-services-metrics"></a>Monitorar as métricas de serviços de mídia 

[O Azure Monitor](../../azure-monitor/overview.md) habilita você a monitorar métricas e logs de diagnóstico que ajudam você a entende como estão o desempenho de seus aplicativos. Para obter uma descrição detalhada desse recurso e para ver por que você iria querer usar logs de diagnóstico e métricas de serviços de mídia do Azure, consulte [logs de diagnóstico e métricas de serviços de mídia do Monitor](media-services-metrics-diagnostic-logs.md).

O Azure Monitor fornece várias maneiras de interagir com as métricas, incluindo gráficos-los no portal, acessá-los por meio da API REST ou consultá-las usando a CLI. Este artigo mostra como monitorar as métricas com os gráficos de portal do Azure e a CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- [Criar uma conta dos Serviços de Mídia](create-account-cli-how-to.md)
- Revisão [os logs de diagnóstico e métricas de serviços de mídia do Monitor](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>Exibir métricas no portal do Azure

1. Entre no Portal do Azure em https://portal.azure.com.
1. Navegue até sua conta dos serviços de mídia do Azure e selecione **métricas**.
1. Clique o **recurso** caixa e selecione o recurso para o qual você deseja monitorar as métricas. 

    O **selecione um recurso** janela aparece à direita com a lista de recursos disponíveis para você. Nesse caso você veja 

    * &lt;Nome da conta de serviços de mídia&gt;
    * &lt;Nome da conta de serviços de mídia&gt;/&lt;nome do ponto de extremidade de streaming&gt;
    * &lt;nome da conta de armazenamento&gt;

    Selecione o recurso e pressione **aplicar**. Para obter detalhes sobre os recursos com suporte e as métricas, consulte [métricas dos serviços de mídia do Monitor](media-services-metrics-diagnostic-logs.md).
 
    ![metrics](media/media-services-metrics/metrics02.png)
    
    > [!NOTE]
    > Para alternar entre os recursos para o qual você deseja monitorar as métricas, clique no **recurso** caixa novamente e repita esta etapa.
1. (Opcionalmente) Nomeie seu gráfico (Editar o nome, pressionando o lápis na parte superior).
1. Adicione métricas que você deseja exibir.

    ![metrics](media/media-services-metrics/metrics03.png)
1. Você pode fixar o gráfico ao seu painel.

## <a name="view-metrics-with-azure-cli"></a>Exibir métricas com a CLI do Azure

Para obter as métricas de "Saída" com a CLI, você executaria o seguinte `az monitor metrics` comando da CLI:

```cli
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Para obter outras métricas, substitua "Saída" para o nome da métrica que lhe interessam.

## <a name="see-also"></a>Consulte também

* [Métricas do Azure Monitor](../../azure-monitor/platform/data-platform.md)
* [Criar, exibir e gerenciar alertas de métrica usando o Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>Próximas etapas

[Logs de diagnóstico](media-services-diagnostic-logs-howto.md)
