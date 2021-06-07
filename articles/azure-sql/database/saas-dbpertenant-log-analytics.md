---
title: Logs do Azure Monitor com um aplicativo multilocatário
description: Configure e use os logs do Azure Monitor com um aplicativo SaaS multilocatário do Banco de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 029e3ba799e5f239bde0ef049316dd268ebe4c8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100588779"
---
# <a name="set-up-and-use-azure-monitor-logs-with-a-multitenant-azure-sql-database-saas-app"></a>Configure e use os logs do Azure Monitor com um aplicativo SaaS multilocatário do Banco de Dados SQL do Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste tutorial, você vai configurar e usar os [logs do Azure Monitor](../../azure-monitor/logs/log-query-overview.md) para monitorar pools elásticos e bancos de dados. Este tutorial se baseia no [Tutorial de gerenciamento e monitoramento do desempenho](saas-dbpertenant-performance-monitoring.md). Este tutorial mostra como usar os logs do Azure Monitor para aumentar o monitoramento e os alertas fornecidos no portal do Azure. Os logs do Azure Monitor são compatíveis com o monitoramento de milhares de pools elásticos e centenas de milhares de bancos de dados. Os logs do Azure Monitor também oferecem uma solução de monitoramento exclusiva que pode integrar o monitoramento de diferentes aplicativos e serviços do Azure entre várias assinaturas do Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Instalar e configurar os logs do Azure Monitor.
> * Usar os logs do Azure Monitor para monitorar pools e bancos de dados.

Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

* O aplicativo de banco de dados por locatário SaaS Wingtip Tickets é implantado. Para implantá-lo em menos de cinco minutos, veja [Implantar e explorar o aplicativo de banco de dados por locatário SaaS Wingtip Tickets](./saas-dbpertenant-get-started-deploy.md).
* O Azure PowerShell está instalado. Para obter mais informações, consulte [Introdução ao Azure PowerShell](/powershell/azure/get-started-azureps).

Consulte o [Tutorial de monitoramento e gerenciamento de desempenho](saas-dbpertenant-performance-monitoring.md) para uma discussão sobre padrões e cenários de SaaS, e como eles afetam os requisitos de uma solução de monitoramento.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-azure-monitor-logs"></a>Monitorar e gerenciar o desempenho do pool elástico e do banco de dados com os logs do Azure Monitor

Para o Banco de Dados SQL do Azure, o monitoramento e o alerta estão disponíveis em bancos de dados e pools no portal do Azure. Esse monitoramento e alertas internos são convenientes, mas também específicos do recurso. Isso significa que são adequados para monitorar grandes instalações ou fornecer uma exibição unificada de recursos e assinaturas.

Em cenários de alto volume, use os logs do Azure Monitor para monitoramento e alertas. O Azure Monitor é um serviço do Azure separado que habilita a análise de logs coletados em um workspace de possivelmente vários serviços. Os logs do Azure Monitor contam com uma linguagem de consulta interna e ferramentas de visualização de dados que permitem a análise de dados operacionais. A solução Análise do SQL fornece várias exibições e consultas de monitoramento e alertas de pools elásticos e bancos de dados. Os logs do Azure Monitor também oferecem um designer de exibição personalizado.

Os workspaces do OMS agora são chamados de workspaces do Log Analytics. Os workspaces do Log Analytics e as soluções de análise são abertas no portal do Azure. O portal do Azure é o ponto de acesso mais novo, mas pode ser o que está por trás do portal do Operations Management Suite em algumas áreas.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Criar dados de diagnóstico de desempenho simulando uma carga de trabalho em seus locatários 

1. No ISE do PowerShell, abra *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Módulos de Aprendizado\\Gerenciamento e Monitoramento do desempenho\\Demo-PerformanceMonitoringAndManagement.ps1*. Mantenha esse script aberto, pois você talvez queira executar vários dos cenários de geração de carga durante este tutorial.
1. Se você já não fez isso, provisione um lote de locatários para tornar o contexto de monitoramento mais interessante. Esse processo leva alguns minutos.

   a. Defina **$DemoScenario = 1,** _Provisionar um lote de locatários_.

   b. Para executar o script e implantar mais 17 locatários, pressione F5.

1. Agora, inicie o gerador de carga para executar uma carga simulada em todos os locatários.

    a. Defina **$DemoScenario = 2**, _Gerar carga de intensidade normal (aproximadamente 30 DTUs)_ .

    b. Para executar o script, pressione F5.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obter os scripts do aplicativo de banco de dados por locatário SaaS Wingtip Tickets

Os scripts e o código-fonte do aplicativo SaaS de banco de dados multilocatário Wingtip Tickets estão disponíveis no repositório [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) do GitHub. Para obter as etapas para fazer o download e desbloquear os scripts de PowerShell do Wingtip Tickets, consulte as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="install-and-configure-log-analytics-workspace-and-the-azure-sql-analytics-solution"></a>Instalar e configurar o workspace do Log Analytics e a solução de Análise de SQL do Azure

O Azure Monitor é um serviço separado que precisa ser configurado. Os logs do Azure Monitor coletam dados de log, telemetria e métricas em um workspace do Log Analytics. Assim como outros recursos no Azure, o espaço de trabalho do Log Analytics deve ser criado. O workspace não precisa ser criado no mesmo grupo de recursos que os aplicativos que ele está monitorando. Mas isso geralmente faz mais sentido. Para o aplicativo Wingtip Tickets, use um único grupo de recursos para garantir que o workspace seja excluído com o aplicativo.

1. No ISE do PowerShell, abra *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Módulos de Aprendizado\\Gerenciamento e Monitoramento do Desempenho\\Log Analytics\\Demo-LogAnalytics.ps1*.
1. Para executar o script, pressione F5.

Agora, abra os logs do Azure Monitor no portal do Azure. Leva alguns minutos para coletar a telemetria no espaço de trabalho do Log Analytics e para torná-la visível. Quanto mais tempo você deixa o sistema coletando dados, mais interessante é a experiência. 

## <a name="use-log-analytics-workspace-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Usar o workspace do Log Analytics e a solução de Análise de SQL para monitorar pools e bancos de dados


Neste exercício, abra o workspace do Log Analytics no portal do Azure para examinar a telemetria coletada dos bancos de dados e os pools.

1. Navegue até o [Portal do Azure](https://portal.azure.com). Selecione **Todos os serviços** para abrir o workspace do Log Analytics. Em seguida, pesquise por Log Analytics.

   ![Workspace do Log Analytics aberto](./media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Selecione o workspace chamado _wtploganalytics-&lt;user&gt;_.

1. Selecione **Visão geral** para abrir a solução Log Analytics no portal do Azure.

   ![Visão geral](./media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Pode demorar alguns minutos até que a solução esteja ativa. 

1. Selecione o bloco **Análise de SQL do Azure** para abri-lo.

    ![Bloco Visão Geral](./media/saas-dbpertenant-log-analytics/overview.png)

1. Os modos de exibição na solução rolam lateralmente, com sua própria barra de rolagem interna na parte inferior. Se necessário, atualize a página.

1. Para explorar a página de resumo, selecione os blocos ou bancos de dados individuais para abrir um gerenciador de detalhes.

    ![Painel do Log Analytics](./media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Altere a configuração de filtro para modificar o intervalo de tempo. Para este tutorial, selecione **Última 1 hora**.

    ![Filtro de tempo](./media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Selecione um banco de dados individual para explorar o uso de consulta e a métrica para esse banco de dados.

    ![Análise de banco de dados](./media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Para ver a métrica de uso, role a página de análise para a direita.
 
     ![Métrica de banco de dados](./media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Role a página de análise para a esquerda e selecione o bloco do servidor na lista **Informações do Recurso**.  

    ![Lista de Informações do Recursos](./media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    Uma página é aberta mostrando os pools e os bancos de dados no servidor.

    ![Servidor com pools e bancos de dados](./media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Selecione um pool. Na página do pool que é aberta, role para a direita a fim de ver as métricas do pool. 

    ![Métricas do pool](./media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


1. Voltando para o espaço de trabalho do Log Analytics, selecione **Portal do OMS** para abrir o espaço de trabalho nele.

    ![Espaço de trabalho do Log Analytics](./media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

No espaço de trabalho do Log Analytics, você poderá explorar ainda mais os dados de métrica e log. 

Nos logs do Azure Monitor, o monitoramento e os alertas se baseiam em consultas em dados no workspace. É o contrário dos alertas definidos em cada recurso no portal do Azure. Baseando alertas em consultas, você pode definir um único alerta sobre todos os bancos de dados em vez de uma definição para cada banco de dados. As consultas são limitadas apenas pelos dados disponíveis no workspace.

Para saber mais sobre como usar os logs do Azure Monitor para consultar e definir alertas, confira [Trabalhar com regras de alertas nos logs do Azure Monitor](../../azure-monitor/alerts/alerts-metric.md).

A cobrança dos logs do Azure Monitor para o Banco de Dados SQL se baseia no volume de dados no workspace. Nesse tutorial, você criou um workspace gratuito, que é limitado a 500 MB por dia. Após esse limite ser atingido, os dados não são mais adicionados ao workspace.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Instalar e configurar os logs do Azure Monitor.
> * Usar os logs do Azure Monitor para monitorar pools e bancos de dados.

Experimente o [Tutorial de análise de locatário](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que aproveitam a implantação inicial do aplicativo de banco de dados por locatário Saas Wingtip Tickets](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Logs do Azure Monitor](../../azure-monitor/insights/azure-sql.md)