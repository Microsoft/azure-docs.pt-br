---
title: Gerenciar um ambiente de Gen 2 – série temporal do Azure | Microsoft Docs
description: Saiba como gerenciar um ambiente Azure Time Series Insights Gen 2.
author: shipra1mishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/15/2020
ms.custom: seodec18
ms.openlocfilehash: cbd28bdf5318bdaf932447f5d1f936d2c614a7f3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103461888"
---
# <a name="manage-azure-time-series-insights-gen2"></a>Gerenciar Azure Time Series Insights Gen2

Depois de criar seu ambiente de Azure Time Series Insights Gen2 usando [o CLI do Azure](./how-to-create-environment-using-cli.md) ou [o portal do Azure](./how-to-create-environment-using-portal.md), você pode modificar suas políticas de acesso e outros atributos de ambiente para atender às suas necessidades de negócios.

## <a name="manage-the-environment"></a>Gerenciar o ambiente

Você pode gerenciar seu ambiente de Azure Time Series Insights Gen2 usando o [portal do Azure](https://portal.azure.com/). Há algumas diferenças importantes entre um ambiente Gen2 e os ambientes Gen1 S1 ou Gen1 S2 para se lembrar ao gerenciar seu ambiente por meio do portal do Azure:

* O painel **visão geral** do portal do Azure Gen2 tem as seguintes alterações:

  * A capacidade é removida porque não se aplica a ambientes Gen2.
  * A propriedade **ID da série temporal** é adicionada. Ela determina como os dados são particionados.
  * Conjuntos de dados de referência são removidos.
  * A URL exibida direciona você para o [Gerenciador de Azure Time Series insights](./concepts-ux-panels.md).
  * O nome da sua conta de Armazenamento do Azure é listado.

* O painel **Configurar** do portal do Azure é removido porque as unidades de escala não se aplicam a ambientes Azure Time Series insights Gen2. No entanto, você pode usar a **configuração de armazenamento** para configurar o armazenamento quente introduzido recentemente.

* O painel de **dados de referência** do portal do Azure é removido em Azure Time Series insights Gen2 porque o conceito de dados de referência foi substituído pelo [TSM (modelo de série temporal)](./concepts-model-overview.md).

:::image type="content" source="media/v2-update-manage/create-and-manage-overview-confirm.png" alt-text="Azure Time Series Insights ambiente Gen2 no portal do Azure":::

## <a name="next-steps"></a>Próximas etapas

* Examinar a lista de [práticas recomendadas de ingestão de streaming](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)
* Entenda como [diagnosticar e solucionar problemas](./how-to-diagnose-troubleshoot.md)
