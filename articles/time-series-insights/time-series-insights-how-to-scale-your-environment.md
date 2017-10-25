---
title: Como dimensionar o ambiente do Azure Time Series Insights | Microsoft Docs
description: Este tutorial aborda como dimensionar seu ambiente do Azure Time Series Insights
keywords: 
services: time-series-insights
documentationcenter: 
author: sandshadow
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/19/2017
ms.author: edett
ms.openlocfilehash: ba6bd1ab05bb7e24dd1bc307218e7a772fbde601
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Como dimensionar o ambiente do Time Series Insights

Este tutorial aborda como dimensionar seu ambiente do Time Series Insights.

> [!NOTE]
> Escalar verticalmente os tipos de sku não é permitido. Um ambiente com um Sku S1 não pode ser convertido em um ambiente de S2.

## <a name="s1-sku-ingress-rates-and-capacities"></a>As taxas e as capacidades de entrada de SKU S1

| Capacidade de SKU S1 | Taxa de entrada | Capacidade de armazenamento máxima
| --- | --- | --- |
| 1 | 1 GB (1 milhão de eventos) | 30 GB (30 milhões de eventos) por mês |
| 10 | 10 GB (10 milhões de eventos) | 300 GB (300 milhões de eventos) por mês |

## <a name="s2-sku-ingress-rates-and-capacities"></a>As taxas e as capacidades de entrada de SKU S2

| Capacidade de SKU S2 | Taxa de entrada | Capacidade de armazenamento máxima
| --- | --- | --- |
| 1 | 10 GB (10 milhões de eventos) | 300 GB (300 milhões de eventos) por mês |
| 10 | 100 GB (100 milhões de eventos) | 3 TB (3 bilhões de eventos) por mês |

As capacidades são dimensionadas linearmente, portanto, uma sku S1 com capacidade 2 dá suporte a 2 GB (2 milhões) de eventos por taxa de entrada por dia e 60 GB (60 milhões de eventos) por mês.

## <a name="changing-the-capacity-of-your-environment"></a>Alterando a capacidade de seu ambiente

1. No portal do Azure, selecione o ambiente cuja capacidade você deseja alterar.
1. Em Configurações, clique em Configurar.
1. Use o controle deslizante Capacidade para selecionar a capacidade que atenda aos requisitos das taxas de entrada e a capacidade de armazenamento.

## <a name="next-steps"></a>Próximas etapas

* Verifique se a nova capacidade é suficiente para evitar a limitação. Para obter mais detalhes, consulte a seção *O ambiente pode estar ficando restrito* [aqui](time-series-insights-diagnose-and-solve-problems.md).