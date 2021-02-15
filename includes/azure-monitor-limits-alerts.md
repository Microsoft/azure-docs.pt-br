---
title: incluir arquivo
description: incluir arquivo
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 10/01/2020
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 0c3cdc21425e0634a725000efb27b3cde0ccd718
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97844762"
---
| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| Alertas de métrica (clássicos) |100 regras de alerta ativas por assinatura. | Contate o suporte |
| Alertas de métricas |5\.000 regras de alerta ativas por assinatura no Azure público, no Azure China 21Vianet e nas nuvens do Azure Government. Se estiver atingindo esse limite, veja se pode usar [alertas de vários recursos do mesmo tipo](../articles/azure-monitor/platform/alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).<br/>Cinco mil séries temporais métricas por regra de alerta. | Contate o suporte. |
| Alertas do log de atividades | 100 regras de alerta ativas por assinatura (não podem ser aumentadas). | Igual ao padrão |
| Alertas de registro | 512 regras de alerta ativas por assinatura. 200 regras de alerta ativas por recurso. | Contate o suporte |
| Comprimento da descrição das Regras de alerta e das Regras de ação| Alertas da pesquisa de logs – 4096 caracteres<br/>Todos os outros – 2048 caracteres | Igual ao padrão |
