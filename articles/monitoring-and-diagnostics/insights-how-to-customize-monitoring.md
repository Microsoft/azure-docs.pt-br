---
title: "Visão Geral das métricas no Microsoft Azure | Microsoft Docs"
description: "Saiba como personalizar gráficos de monitoramento no Azure."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c36031eb-4df5-4cd5-9479-311d493a40d2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: robb
ms.openlocfilehash: 3f9ebb0f5737714dd685f0dcc1ff4b1c0c89528f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-metrics-in-microsoft-azure"></a>Visão Geral das métricas no Microsoft Azure
Todos os serviços do Azure monitoram métricas-chave que permitem que você monitore a integridade, o desempenho, a disponibilidade e o uso de seus serviços. Você pode exibir essas métricas no portal do Azure e também pode usar a [API REST](https://msdn.microsoft.com/library/azure/dn931930.aspx) ou o [SDK .NET](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) para acessar o conjunto completo de métricas programaticamente.

Para alguns serviços, talvez seja necessário ativar o diagnóstico para ver as métricas. Para outros, como máquinas virtuais, você obterá um conjunto básico de métricas, mas precisa habilitar o conjunto completo de métricas de alta frequência. Consulte [Habilitar monitoramento e diagnóstico](insights-how-to-use-diagnostics.md) para obter mais informações.

## <a name="using-monitoring-charts"></a>Usando gráficos de monitoramento
Você pode criar um gráfico com as métricas relativas a qualquer período de tempo escolhido.

1. No [Portal do Azure](https://portal.azure.com/), clique em **Navegar**e, em seguida, clique em um recurso que você esteja interessado em monitorar.
2. A seção **Monitoramento** contém as métricas mais importantes para cada recurso do Azure. Por exemplo, um aplicativo Web tem **Solicitações e erros**, enquanto que uma máquina virtual teria **Percentual de CPU** e **Leitura e gravação de disco**: ![Lente de monitoramento](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)
3. Clicar em qualquer gráfico mostrará a folha **Métrica** . Na folha, além do gráfico, há uma tabela que mostra as agregações das métricas (como média, mínima e máxima, para o intervalo de tempo que você escolheu). Abaixo disso estão as regras de alerta para o recurso.
    ![Folha Métrica](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)
4. Para personalizar as linhas que aparecem, clique no botão **Editar** no gráfico ou no comando **Editar gráfico** na folha Métrica.
5. Na folha Editar consulta, você pode fazer três coisas:
   
   * Alterar o intervalo de tempo
   * Trocar a aparência das barras e linhas
   * Escolher métricas diferentes ![Editar consulta](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)
6. A alteração do intervalo de tempo é tão fácil quanto selecionar um intervalo diferente (como **Última Hora**) e clicar em **Salvar** na parte inferior da folha. Você também pode escolher **Personalizar**, permite escolher qualquer período de tempo nas duas últimas semanas. Por exemplo, você pode ver as duas semanas inteiras ou apenas uma hora do dia anterior. Digite na caixa de texto para inserir outra hora.
    ![Intervalo de tempo personalizado](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)
7. Abaixo do intervalo de tempo, você pode escolher qualquer número de métricas para mostrar no gráfico.
8. Quando você clica em Salvar, suas alterações serão salvas para esse recurso em particular. Por exemplo, se você tiver duas máquinas virtuais, e alterar um gráfico em uma delas, isso não afetará a outra.

## <a name="creating-side-by-side-charts"></a>Criando gráficos lado a lado
Com a poderosa personalização no portal, você pode adicionar quantos gráficos quiser.

1. No menu **...** na parte superior da folha, clique em **Adicionar blocos**:  
    ![Adicionar menu](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. Em seguida, você pode selecionar um gráfico na **Galeria** no lado direito da tela: ![Galeria](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. Se você não vir a métrica desejada, você sempre poderá adicionar uma das métricas predefinidas e **Editar** o gráfico para mostrar a métrica que você precisa.

## <a name="monitoring-usage-quotas"></a>Monitorando cotas de uso
A maioria das métricas mostra tendências ao longo do tempo, mas determinados dados, como cotas de uso, são informações específicas com um limite.

Você também pode ver as cotas de uso na folha para recursos que têm cotas:

![Uso](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)

Assim como com as métricas, você pode usar a [API REST](https://msdn.microsoft.com/library/azure/dn931963.aspx) ou o [SDK .NET](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) para acessar o conjunto completo de cotas de uso programaticamente.

## <a name="next-steps"></a>Próximas etapas
* [Receba notificações de alerta](insights-receive-alert-notifications.md) sempre que uma métrica cruzar um limite.
* [Habilite o monitoramento e diagnóstico](insights-how-to-use-diagnostics.md) para coletar métricas detalhadas de alta frequência em seu serviço.
* [Dimensione a contagem de instâncias automaticamente](insights-how-to-scale.md) para se certificar de que o serviço está disponível e responsivo.
* [Monitore o desempenho do aplicativo](../application-insights/app-insights-azure-web-apps.md) se você quiser compreender exatamente como seu código está sendo executado na nuvem.
* Use os [aplicativos e páginas da Web do Application Insights para JavaScript](../application-insights/app-insights-web-track-usage.md) para obter a análise do cliente sobre os navegadores que visitam uma página da Web.
* [Monitore a disponibilidade e a capacidade de resposta de qualquer página da Web](../application-insights/app-insights-monitor-web-app-availability.md) com o Application Insights para que você possa descobrir se a página está inativa.

