---
title: Configurar alertas de monitoramento para trabalhos do Azure Stream Analytics
description: Este artigo descreve como usar o Portal do Azure para configurar o monitoramento e alertas para trabalhos do Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/05/2019
ms.custom: seodec18
ms.openlocfilehash: 52db8217cc1e1f84d25ab896be9b42db3bf6bd81
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771882"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Configurar alertas para trabalhos do Stream Analytics do Azure

É importante monitorar o trabalho do Azure Stream Analytics para garantir que ele seja executado continuamente sem problemas. Este artigo descreve como configurar alertas para cenários comuns que devem ser monitorados. 

As regras podem ser configuradas em métricas por meio do portal e [de forma programática](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) nos dados dos Logs de Operação.

## <a name="set-up-alerts-in-the-azure-portal"></a>Configurar alertas no Portal do Azure

O exemplo a seguir demonstra como configurar alertas para os casos em que o trabalho entra em um estado com falha. Esse alerta é recomendado para todos os trabalhos.

1. No Portal do Azure, abra o trabalho do Stream Analytics para o qual você deseja criar um alerta.

2. Na página **Trabalho**, navegue para a seção **Monitoramento**.  

3. Selecione **Métricas** e, em seguida, clique em **Nova regra de alerta**.

   ![Configuração de alertas do Stream Analytics do portal do Azure](./media/stream-analytics-set-up-alerts/stream-analytics-set-up-alerts.png)  

4. O nome do trabalho do Stream Analytics deve ser exibido automaticamente em **RECURSO**. Clique em **Adicionar condição** e selecione **Todas as operações Administrativas** em **Configurar lógica de sinal**.

   ![Selecionar o nome do sinal para o alerta do Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-condition-signal.png)  

5. Em **Configurar lógica de sinal**, altere **Nível de Evento** para **Todos** e altere **Status** para **Falha**. Deixe a opção **Evento iniciado por** em branco e clique em **Concluído**.

   ![Configurar a lógica de sinal para o alerta do Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-configure-signal-logic.png) 

6. Selecione um grupo de ações existente ou crie um grupo. Neste exemplo, um grupo de ações chamado **TIDashboardGroupActions** foi criado com uma ação **Emails** que envia um email para os usuários com a Função **Proprietário** do Azure Resource Manager.

   ![Configurar um alerta para um trabalho do Azure Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-add-group-email-action.png)

7. O **RECURSO**, a **CONDIÇÃO** e os **GRUPOS DE AÇÕES** devem ter uma entrada cada um. Observe que, para que os alertas sejam disparados, as condições definidas precisam ser atendidas. Por exemplo, você pode medir o valor médio de uma métrica nos últimos 15 minutos, a cada 5 minutos.

   ![Criar regra de alerta do Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule-2.png)

   Adicione um **Nome da regra de alerta**, uma **Descrição** e o **Grupo de Recursos** aos **DETALHES DO ALERTA** e clique em **Criar regra de alerta** para criar a regra para o trabalho do Stream Analytics.

   ![Criar regra de alerta do Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule.png)
   
## <a name="scenarios-to-monitor"></a>Cenários a serem monitorados

Os alertas a seguir são recomendados para o monitoramento do desempenho do trabalho do Stream Analytics. Estas métricas devem ser avaliadas a cada minuto no período dos últimos 5 minutos.

|Métrica|Condição|Agregação de Tempo|Limite|Ações Corretivas|
|-|-|-|-|-|
|% de Utilização da UA|Maior que|Máximo|80|Há vários fatores que aumentam a % de Utilização da UA. Você pode dimensionar com a paralelização de consultas ou aumentar o número de unidades de streaming. Para obter mais informações, confira [Aproveitar a paralelização de consultas no Azure Stream Analytics](stream-analytics-parallelization.md).|
|Erros em tempo de execução|Maior que|Total|0|Examine os logs de atividades ou de diagnóstico e faça as alterações apropriadas nas entradas, na consulta ou nas saídas.|
|Atraso de marca-d'água|Maior que|Máximo|Quando o valor médio dessa métrica nos últimos 15 minutos é maior que a tolerância da chegada tardia (em segundos). Se você não modificou a tolerância da chegada tardia, o padrão é definido como 5 segundos.|Tente aumentar o número de UAs ou paralelizar a consulta. Para obter mais informações sobre as UAs, confira [Compreender e ajustar unidades de streaming](stream-analytics-streaming-unit-consumption.md#how-many-sus-are-required-for-a-job). Para obter mais informações sobre como paralelizar a consulta, confira [Aproveitar a paralelização de consultas no Azure Stream Analytics](stream-analytics-parallelization.md).|
|Erros de desserialização de entrada|Maior que|Total|0|Examine os logs de atividades ou de diagnóstico e faça as alterações apropriadas na entrada. Para obter mais informações sobre logs de diagnóstico, confira [Solução de problemas do Azure Stream Analytics usando logs de diagnóstico](stream-analytics-job-diagnostic-logs.md)|

## <a name="get-help"></a>Obter ajuda

Para obter mais detalhes sobre como configurar alertas no portal do Azure, consulte [Receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  

Para obter mais assistência, experimente nosso [fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

