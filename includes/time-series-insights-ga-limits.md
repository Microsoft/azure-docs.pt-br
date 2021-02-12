---
title: incluir arquivo
description: incluir arquivo
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 07/09/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: e584b6eff16636f0657c586f6c630dbf8bbb99b2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026817"
---
O a seguir resume os limites principais no Azure Time Series Insights Gen1.

### <a name="sku-ingress-rates-and-capacities"></a>Taxas e capacidades de entrada do SKU

As taxas e as capacidades de entrada de SKU S1 e S2 fornecem flexibilidade ao configurar um novo ambiente de Azure Time Series Insights. Sua capacidade de SKU indica sua taxa de entrada diária com base no número de eventos ou bytes armazenados, o que ocorrer primeiro. Observe que a entrada é medida *por minuto* e a **limitação** é aplicada usando o algoritmo de Bucket de tokens. A entrada é medida em blocos de 1 KB. Por exemplo, um evento real de 0,8-KB seria medido como um evento, e um evento de 2,6 KB é contado como três eventos.

| Capacidade de SKU S1 | Taxa de entrada | Capacidade máxima de armazenamento
| --- | --- | --- |
| 1 | 1 GB (1 milhão eventos) por dia | 30 GB (30 milhões de eventos) por mês |
| 10 | 10 GB (10 milhões eventos) por dia | 300 GB (300 milhões de eventos) por mês |

| Capacidade de SKU S2 | Taxa de entrada | Capacidade máxima de armazenamento
| --- | --- | --- |
| 1 | 10 GB (10 milhões eventos) por dia | 300 GB (300 milhões de eventos) por mês |
| 10 | 100 GB (100 milhões eventos) por dia | 3 TB (3 bilhões de eventos) por mês |

> [!NOTE]
> As capacidades são dimensionadas linearmente, portanto, uma SKU S1 com capacidade 2 dá suporte a 2 GB (2 milhões) de eventos por taxa de entrada por dia e 60 GB (60 milhões de eventos) por mês.

Os ambientes de SKU S2 dão suporte a substancialmente mais eventos por mês e têm uma capacidade de entrada significativamente mais alta.

| SKU  | Contagem de eventos por mês  | Contagem de eventos por minuto | Tamanho do evento por minuto  |
|---------|---------|---------|---------|---------|
| S1     |   30 milhões   |  720    |  720 KB   |
 |S2     |   300 milhões   | 7.200   | 7.200 KB  |

### <a name="property-limits"></a>Limites de propriedade

Os limites de propriedade Gen1 dependem do ambiente de SKU selecionado. As propriedades de evento fornecidas têm as colunas JSON, CSV e de gráfico correspondentes que podem ser exibidas no [Gerenciador de Azure Time Series insights](../articles/time-series-insights/time-series-quickstart.md).

| SKU | Propriedades máximas |
| --- | --- |
| S1 | Propriedades de 600 (colunas) |
| S2 | Propriedades de 800 (colunas) |

### <a name="event-sources"></a>Origens de eventos

Há suporte para no máximo duas origens de evento por instância.

* Saiba como [Adicionar uma origem do hub de eventos](../articles/time-series-insights/how-to-ingest-data-event-hub.md).
* Configurar [uma origem do Hub IOT](../articles/time-series-insights/how-to-ingest-data-iot-hub.md).

### <a name="api-limits"></a>Limites de API

Os limites da API REST para Azure Time Series Insights Gen1 são especificados na [documentação de referência da API REST](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability).