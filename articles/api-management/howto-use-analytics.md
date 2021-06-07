---
title: Usar a análise de API no gerenciamento de API do Azure | Microsoft Docs
description: Use a análise no gerenciamento de API do Azure para ajudá-lo a entender e categorizar o uso de APIs e o desempenho da API.
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 11/24/2020
ms.author: apimpm
ms.openlocfilehash: ca7bd70bbf99a6d0079717a7a02328b11528d2e0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96841368"
---
# <a name="get-api-analytics-in-azure-api-management"></a>Obter análise de API no gerenciamento de API do Azure

O gerenciamento de API do Azure fornece análise interna para suas APIs. Analise o uso e o desempenho das APIs em sua instância de gerenciamento de API em várias dimensões, incluindo:

* Hora
* painel Geografia do app&#39;s selecionado
* APIs
* Operações de API
* Produtos
* Assinaturas
* Usuários
* Requests

:::image type="content" source="media/howto-use-analytics/analytics-report-portal.png" alt-text="Análise de linha do tempo no portal":::

Use a análise para monitoramento de alto nível e solução de problemas de suas APIs. Para recursos de monitoramento adicionais, incluindo métricas em tempo real e logs de recursos para diagnóstico e auditoria, consulte [tutorial: monitorar APIs publicadas](api-management-howto-use-azure-monitor.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="analytics---portal"></a>Análise-Portal

Use o portal do Azure para examinar os dados de análise em um relance para sua instância de gerenciamento de API.

1. No [portal do Azure](https://portal.azure.com), navegue até a instância do Gerenciamento de API. 
1. No menu à esquerda, em **monitoramento**, selecione **análise**.

    :::image type="content" source="media/howto-use-analytics/monitoring-menu-analytics.png" alt-text="Selecionar análise para instância de gerenciamento de API no portal":::  
1. Selecione um intervalo de tempo para dados ou insira um intervalo de tempo personalizado.
1. Selecione uma categoria de relatório para dados de análise, como **linha do tempo**, **geografia** e assim por diante.
1. Opcionalmente, filtre o relatório por uma ou mais categorias adicionais.

## <a name="analytics---rest-api"></a>Análise-API REST

Use as operações de [relatórios](/rest/api/apimanagement/2019-12-01/reports) na API REST de gerenciamento de API para recuperar e filtrar dados de análise para sua instância de gerenciamento de API.

As operações disponíveis retornam os registros de relatório por API, geografia, operações de API, produto, solicitação, assinatura, tempo ou usuário.

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução aos recursos do Azure Monitor no gerenciamento de API, consulte [tutorial: monitorar APIs publicadas](api-management-howto-use-azure-monitor.md)
* Para logs e monitoramento de HTTP detalhados, consulte [monitorar suas APIs com o gerenciamento de API do Azure, hubs de eventos e Moesif](api-management-log-to-eventhub-sample.md).
* Saiba mais sobre como integrar o [Gerenciamento de API do Azure com o aplicativo Azure insights](api-management-howto-app-insights.md).