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
ms.openlocfilehash: 071f2849a877f4ea1e8a84eff6ccfb8343be3ec7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101734004"
---
| Recurso | Limite padrão | Limite máximo |
| --- | --- | --- |
| Alertas de métrica (clássicos) |100 regras de alerta ativas por assinatura. | Contate o suporte |
| Alertas de métricas |5\.000 regras de alerta ativas por assinatura no Azure público, no Azure China 21Vianet e nas nuvens do Azure Government. Se estiver atingindo esse limite, veja se pode usar [alertas de vários recursos do mesmo tipo](../articles/azure-monitor/alerts/alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).<br/>Cinco mil séries temporais métricas por regra de alerta. | Contate o suporte. |
| Alertas do log de atividades | 100 regras de alerta ativas por assinatura (não podem ser aumentadas). | Igual ao padrão |
| Alertas de registro | 512 regras de alerta ativas por assinatura. 200 regras de alerta ativas por recurso. | Contate o suporte |
| Comprimento da descrição das Regras de alerta e das Regras de ação| Alertas da pesquisa de logs – 4096 caracteres<br/>Todos os outros – 2048 caracteres | Igual ao padrão |