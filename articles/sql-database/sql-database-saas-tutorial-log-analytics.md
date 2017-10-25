---
title: "Usar o Log Analytics com um aplicativo multilocatário de Banco de Dados SQL | Microsoft Docs"
description: "Configurar e usar o Log Analytics (OMS) com um aplicativo SaaS multilocatário do Banco de Dados SQL do Azure"
keywords: tutorial do banco de dados SQL
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: billgib; sstein
ms.openlocfilehash: f9428871eab19f56e439ba529a9dcfde833d4fbf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="setup-and-use-log-analytics-oms-with-a-multi-tenant-azure-sql-database-saas-app"></a>Configurar e usar o Log Analytics (OMS) com um aplicativo SaaS multilocatário do Banco de Dados SQL do Azure

Neste tutorial, você configura e usa o *Log Analytics ([OMS](https://www.microsoft.com/cloud-platform/operations-management-suite))* para monitorar pools elásticos e bancos de dados. Este tutorial se baseia no [tutorial de Gerenciamento e Monitoramento do Desempenho](sql-database-saas-tutorial-performance-monitoring.md). Ele mostra como usar o *Log Analytics* para ampliar o monitoramento e os alertas fornecidos no portal do Azure. O Log Analytics é adequado para monitoramento e alertas em grande escala, porque ele dá suporte a centenas de pools e centenas de milhares de bancos de dados. Ele também fornece uma única solução de monitoramento, que pode integrar o monitoramento de diferentes aplicativos e serviços do Azure, entre várias assinaturas do Azure.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Instalar e configurar o Log Analytics (OMS)
> * Usar o Log Analytics para monitorar pools e bancos de dados

Para concluir este tutorial, verifique se todos os pré-requisitos a seguir são atendidos:

* O aplicativo SaaS Wingtip é implantado. Para implantar em menos de cinco minutos, confira [Implantar e explorar o aplicativo de SaaS do Wingtip](sql-database-saas-tutorial.md)
* O Azure PowerShell está instalado. Para obter detalhes, consulte [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

Consulte o [Tutorial de monitoramento e gerenciamento de desempenho](sql-database-saas-tutorial-performance-monitoring.md) para uma discussão sobre padrões e cenários de SaaS, e como eles afetam os requisitos de uma solução de monitoramento.

## <a name="monitoring-and-managing-performance-with-log-analytics-oms"></a>Monitoramento e gerenciamento de desempenho com o Log Analytics (OMS)

Para o Banco de Dados SQL, o monitoramento e o alerta estão disponíveis em bancos de dados e pools. Esse monitoramento e alerta internos são específicos de recursos e são convenientes para pequenas quantidades de recursos, mas menos adequados para monitoramento de grandes instalações ou para fornecer uma exibição unificada entre diferentes assinaturas e recursos.

Para cenários de alto volume, o Log Analytics pode ser usado. Esse é um serviço separado do Azure que fornece análise sobre telemetria e logs de diagnóstico emitidos, coletados em um espaço de trabalho de Log Analytics, que pode coletar a telemetria de muitos serviços e ser usado para consultar e definir alertas. O Log Analytics fornece uma linguagem de consulta e ferramentas de visualização de dados internas que permitem a visualização e análise de dados operacionais. A solução de Análise de SQL fornece várias exibições e consultas de monitoramento e alertas predefinidos de pool elástico e de banco de dados de e permite que você adicione suas próprias consultas ad hoc e salve-as conforme necessário. O OMS também fornece um designer de exibição personalizado.

Os espaços de trabalho e as soluções de análise do Log Analytics podem ser abertos no Portal do Azure e no OMS. O Portal do Azure é o ponto de acesso mais recente, mas pode estar por trás do portal do OMS em algumas áreas.

### <a name="create-data-by-starting-the-load-generator"></a>Criar dados iniciando o gerador de carga 

1. Abra **Demo-PerformanceMonitoringAndManagement.ps1** no **ISE do PowerShell**. Mantenha esse script aberto pois você talvez queira executar vários dos cenários de geração de carga durante este tutorial.
1. Se você tem menos de cinco locatários, provisione um lote de locatários para oferecer um contexto de monitoramento mais interessante:
   1. Defina **$DemoScenario = 1,** **Provisionar um lote de locatários**
   1. Para executar o script, pressione **F5**.

1. Defina **$DemoScenario** = 2, **Gerar carga de intensidade normal (aproximadamente 40 DTU)**.
1. Para executar o script, pressione **F5**.

## <a name="get-the-wingtip-application-scripts"></a>Obter os scripts do aplicativo Wingtip

Os scripts do Wingtip Tickets e o código-fonte do aplicativo estão disponíveis no repositório GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Os arquivos de script estão localizados na [pasta de Módulos de Aprendizado](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Baixe a pasta **Módulos de Aprendizado** em seu computador local, mantendo a estrutura de pastas.

## <a name="installing-and-configuring-log-analytics-and-the-azure-sql-analytics-solution"></a>Instalando e configurando o Log Analytics e a solução Análise de SQL do Azure

O Log Analytics é um serviço separado que precisa ser configurado. O Log Analytics coleta dados de log e telemetria e métrica em um espaço de trabalho de Log Analytics. Um espaço de trabalho é um recurso, assim como outros recursos no Azure e deve ser criado. Embora o espaço de trabalho não precise ser criado no mesmo grupo de recursos que o(s) aplicativo(s) que ele está monitorando, isso geralmente faz mais sentido. Para o aplicativo SaaS Wingtip, isso permite que o espaço de trabalho seja excluído facilmente com o aplicativo ao simplesmente excluir o grupo de recursos.

1. Abra ...\\Módulos de aprendizado\\Monitoramento e gerenciamento de desempenho\\Log Analytics\\*Demo-LogAnalytics.ps1* no **ISE do PowerShell**.
1. Para executar o script, pressione **F5**.

Nesse ponto, você deve ser capaz de abrir o Log Analytics no Portal do Azure (ou no portal do OMS). Leva alguns minutos para que a telemetria seja coletada no espaço de trabalho do Log Analytics e se torne visível. Quanto mais tempo você deixa o sistema coletando de dados, mais interessante é a experiência. Agora é um bom momento para pegar algo pra beber. Só dê uma olhadinha para ter certeza de que o gerador de carga ainda está em execução!


## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Usar o Log Analytics e a solução de Análise de SQL para monitorar pools e bancos de dados


Neste exercício, abra o Log Analytics e o portal do OMS para examinar a telemetria que está sendo coletada para os bancos de dados e pools.

1. Navegue até o [Portal do Azure](https://portal.azure.com) e abra o Log Analytics clicando em Mais serviços e, em seguida, pesquise o Log Analytics:

   ![abrir log analytics](media/sql-database-saas-tutorial-log-analytics/log-analytics-open.png)

1. Selecione o espaço de trabalho chamado *wtploganalytics-&lt;USER&gt;*.

1. Selecione **Visão geral** para abrir a solução Log Analytics no Portal do Azure.
   ![overview-link](media/sql-database-saas-tutorial-log-analytics/click-overview.png)

    **IMPORTANTE**: pode levar alguns minutos até que a solução esteja ativa. Tenha paciência!

1. Clique no bloco Análise de SQL do Azure para abri-lo.

    ![visão geral](media/sql-database-saas-tutorial-log-analytics/overview.png)

    ![análise](media/sql-database-saas-tutorial-log-analytics/analytics.png)

1. A exibição na folha da solução rola para os lados através da sua própria barra de rolagem na parte inferior (atualize a folha se necessário).

1. Explore as várias exibições clicando nelas ou em recursos individuais para abrir um gerenciador de detalhamento, no qual você pode usar o controle deslizante de tempo na parte superior esquerda ou clicar em uma barra vertical para focalizar em uma fração de tempo mais estreita. Com esta exibição, você pode selecionar bancos de dados individuais ou pools para se concentrar em recursos específicos:

    ![gráfico](media/sql-database-saas-tutorial-log-analytics/chart.png)

1. De volta na folha da solução, se você rolar para a extrema direita, verá algumas consultas salvas nas quais você pode clicar para abrir e explorar. Você pode tentar modificar essas consultas e salvar as consultas interessantes que produzir, as quais você poderá abrir novamente e usar com outros recursos.

1. Voltando para a folha do espaço de trabalho do Log Analytics, selecione Portal do OMS para abrir a solução nele.

    ![oms](media/sql-database-saas-tutorial-log-analytics/oms.png)

1. No portal do OMS, você pode configurar alertas. Clique na parte de alertas do modo de exibição de DTU do Banco de Dados.

1. Na exibição Pesquisa de Logs que aparece, você verá um gráfico de barras com as métricas que estão sendo representadas.

    ![pesquisa do log](media/sql-database-saas-tutorial-log-analytics/log-search.png)

1. Ao clicar em Alerta na barra de ferramentas, você verá a configuração de alertas e poderá alterá-la.

    ![adicionar regra de alerta](media/sql-database-saas-tutorial-log-analytics/add-alert.png)

O monitoramento e os alertas no Log Analytics e no OMS baseiam-se em consultas sobre os dados no espaço de trabalho, ao contrário dos alertas em cada folha de recursos, que são específicos de recursos. Portanto, você pode definir um alerta que examina todos os bancos de dados, digamos, em vez de definir um alerta por banco de dados. Ou escrever um alerta que usa uma consulta composta sobre vários tipos de recurso. As consultas são limitadas apenas pelos dados disponíveis no espaço de trabalho.

O Log Analytics para o Banco de Dados SQL é cobrado com base no volume de dados no espaço de trabalho. Nesse tutorial, você criou um espaço de trabalho Gratuito, que é limitado a 500 MB por dia. Quando esse limite é atingido, os dados não são mais adicionados ao espaço de trabalho.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Instalar e configurar o Log Analytics (OMS)
> * Usar o Log Analytics para monitorar pools e bancos de dados

[Tutorial de análise de locatário](sql-database-saas-tutorial-tenant-analytics.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que aproveitam a implantação de aplicativo SaaS Wingtip inicial](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
* [OMS](https://blogs.technet.microsoft.com/msoms/2017/02/21/azure-sql-analytics-solution-public-preview/)
