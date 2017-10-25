---
title: Configurar alertas para consultas no Stream Analytics | Microsoft Docs
description: "Noções básicas sobre alertas do Stream Analytics"
keywords: configurar alertas
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 9952e2cf-b335-4a5c-8f45-8d3e1eda2e20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/26/2017
ms.author: samacha
ms.openlocfilehash: cdbd7da36f3eb2795fc4548915ebbf18200ca44d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Configurar alertas para trabalhos do Stream Analytics do Azure
## <a name="introduction-monitor-page"></a>Introdução: Página do monitor
Você pode configurar alertas para disparar um alerta quando uma métrica atinge uma condição que você especifica. Por exemplo, você pode configurar um alerta para uma condição semelhante à seguinte:

`If there are zero input events in the last 5 minutes, send email notification to sa-admin@example.com`

As regras podem ser configuradas em métricas por meio do portal ou [de modo programático](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) pelos dados dos Logs de Operação.

## <a name="set-up-alerts-in-the-azure-portal"></a>Configurar alertas no Portal do Azure
1. No Portal do Azure, abra o trabalho do Stream Analytics para o qual você deseja criar um alerta. 

2. Na folha **Trabalho**, clique na seção **Monitoramento**.  

3. Na folha **Métrica**, clique no comando **Adicionar alerta**.

      ![Configuração do portal do Azure](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

4. Insira um nome e uma descrição.

5. Use os seletores para definir a condição sob a qual o alerta será enviado.

6. Forneça informações sobre qual deve ser o destino do alerta.

      ![Configurar um alerta para um trabalho do Azure Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-add-alert.png)  

Para obter mais detalhes sobre como configurar alertas no portal do Azure, consulte [Receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  


## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

