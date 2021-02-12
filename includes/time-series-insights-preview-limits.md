---
title: incluir arquivo
description: incluir arquivo
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 016ad0e11f3378dba887e0a235f235fa91e3aa03
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98109297"
---
### <a name="property-limits"></a>Limites de propriedade

Azure Time Series Insights os limites de propriedade aumentaram para 1.000 para o armazenamento quente e nenhum limite de propriedade para o armazenamento frio. As propriedades de evento fornecidas têm as colunas JSON, CSV e de gráfico correspondentes que você pode exibir dentro do [Azure Time Series insights Gen2 Explorer](../articles/time-series-insights/quickstart-explore-tsi.md).

| SKU | Propriedades máximas |
| --- | --- |
| Gen2 (L1) | 1.000 Propriedades (colunas) para armazenamento quente e ilimitado para armazenamento frio|
| Gen1 (S1) | Propriedades de 600 (colunas) |
| Gen1 (S2) | Propriedades de 800 (colunas) |

### <a name="streaming-ingestion"></a>Ingestão de streaming

* Há um máximo de duas [origens de eventos](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md) por ambiente.

* As práticas recomendadas e as diretrizes gerais para fontes de eventos podem ser encontradas [aqui](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)

* Por padrão, Azure Time Series Insights Gen2 pode ingerir dados de entrada a uma taxa de **até 1 megabyte por segundo (Mbps) por Azure Time Series insights ambiente do Gen2**. Há limitações adicionais [por partição de hub](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits). Taxas de até 2 MBps podem ser fornecidas enviando um tíquete de suporte por meio do portal do Azure. Para saber mais, leia [limites de taxa de transferência de ingestão de streaming](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md).

### <a name="api-limits"></a>Limites de API

Os limites da API REST para Azure Time Series Insights Gen2 são especificados na [documentação de referência da API REST](/rest/api/time-series-insights/preview#limits-1).