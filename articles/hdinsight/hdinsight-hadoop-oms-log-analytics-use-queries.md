---
title: Consultar o Azure Log Analytics para monitorar clusters Azure HDInsight | Microsoft Docs
description: "Saiba como executar consultas no Azure Log Analytics para monitorar trabalhos que estão sendo executados em um cluster HDInsight."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: nitinme
ms.openlocfilehash: 8fe91bed69a1c06367346041d8caba4aaee4c82a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters-preview"></a>Consultar o Log Analytics do Azure para monitorar clusters HDInsight (versão prévia)

Neste artigo, você examinará alguns cenários sobre como usar o Azure Log Analytics com clusters Azure HDInsight. Os três cenários mais comuns são:

* Analisar métricas de cluster HDInsight no OMS
* Pesquisar mensagens de log para clusters HDInsight
* Criar alertas baseados em eventos que ocorrem nos clusters

## <a name="prerequisites"></a>Pré-requisitos

* É necessário ter configurado um cluster HDInsight para usar o Azure Log Analytics. Para obter instruções, consulte [Use Azure Log Analytics with HDInsight clusters](hdinsight-hadoop-oms-log-analytics-tutorial.md) (Usar o Azure Log Analytics com clusters HDInsight).

* Você deverá ter adicionado as soluções de gerenciamento específicas para o cluster HDInsight ao espaço de trabalho do OMS conforme descrito em [Add HDInsight cluster management solutions to Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md) (Adicionar soluções de gerenciamento do cluster HDInsight ao Log Analytics).

## <a name="analyze-hdinsight-cluster-metrics-in-oms"></a>Analisar métricas de cluster HDInsight no OMS

Nesta seção, percorreremos as etapas para procurar métricas específicas para seu cluster HDInsight.

1. Abra o painel do OMS. No Portal do Azure, abra a folha de cluster HDInsight associada ao Azure Log Analytics, clique na guia Monitoramento e clique em **Abrir painel do OMS**.

    ![Abrir painel do OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Abrir painel do OMS")

2. No painel do OMS, na tela inicial, clique em **Pesquisa de logs**.

    ![Abrir pesquisa de logs](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Abrir pesquisa de logs")

3. Na janela Pesquisa de logs, na caixa de texto **Iniciar pesquisa aqui**, digite `*` para pesquisar todas as métricas para todas as métricas disponíveis para todos os clusters HDInsight configurados para usar o Azure Log Analytics. Pressione ENTER.

    ![Pesquisar todas as métricas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Pesquisar todas as métricas")

4. Você deverá ver uma saída como a seguinte.

    ![Pesquisar saída de todas as métricas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "Pesquisar saída de todas as métricas")

5. No painel esquerdo, na categoria **Tipo**, pesquise uma métrica que você deseja ver de maneira aprofundada. Neste tutorial, vamos utilizar `metrics_resourcemanager_queue_root_default_CL`. Selecione a caixa de seleção correspondente à métrica e, em seguida, clique em **Aplicar**.

    > [!NOTE]
    > Talvez você precise clicar no botão **[+] Mais** para localizar a métrica que você está procurando. Além disso, o botão **Aplicar** está na parte inferior da lista. Portanto, você deve rolar para baixo para vê-lo.
    > 
    >    
    Observe que a consulta na caixa de texto agora muda para a mostrada na caixa realçada na captura de tela a seguir:

    ![Pesquisar métricas específicas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Pesquisar métricas específicas")

6. Agora é possível obter detalhes sobre esta métrica específica. Por exemplo, agora é possível refinar a saída existente com base na média de recursos usados em um intervalo de 10 minutos, categorizada por nome do cluster. Digite a seguinte consulta na caixa de texto de consulta.

        * (Type=metrics_resourcemanager_queue_root_default_CL) | measure avg(UsedAMResourceMB_d) by ClusterName_s interval 10minute

    ![Pesquisar métricas específicas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-more-specific-metrics.png "Pesquisar métricas específicas")

7. Em vez de refinar com base na média de recursos usados, é possível usar a seguinte consulta para refinar os resultados com base em quando os recursos máximos foram usados (assim como o 90º e o 95º percentil) em uma janela de 10 minutos.

        * (Type=metrics_resourcemanager_queue_root_default_CL) | measure max(UsedAMResourceMB_d) , pct95(UsedAMResourceMB_d), pct90(UsedAMResourceMB_d)  by ClusterName_s interval 10minute

    ![Pesquisar métricas específicas](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-more-specific-metrics-1.png "Pesquisar métricas específicas")

## <a name="search-for-specific-log-messages-in-hdinsight-clusters"></a>Pesquisar mensagens de log específicas em clusters HDInsight

Nesta seção, percorreremos as etapas para examinar mensagens de erro durante uma janela de tempo específica. Estas etapas são apenas um exemplo de como você pode chegar à mensagem de erro na qual você está interessado. É possível usar qualquer propriedade disponível para procurar os erros que você está tentando localizar.

1. Abra o painel do OMS. No Portal do Azure, abra a folha de cluster HDInsight associada ao Azure Log Analytics, clique na guia Monitoramento e clique em **Abrir painel do OMS**.

    ![Abrir painel do OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Abrir painel do OMS")

2. No painel do OMS, na tela inicial, clique em **Pesquisa de logs**.

    ![Abrir pesquisa de logs](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Abrir pesquisa de logs")

3. Na janela Pesquisa de logs, na caixa de texto **Iniciar pesquisa aqui**, digite `"Error"` (com aspas) para pesquisar todas as mensagens de erro para todos os clusters HDInsight configurados para usar o Azure Log Analytics. Pressione ENTER.

    ![Pesquisar todos os erros](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors.png "Pesquisar todos os erros")

4. Você deverá ver uma saída como a seguinte.

    ![Pesquisar a saída de todos os erros](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "Pesquisar a saída de todos os erros")

5. No painel esquerdo, na categoria **Tipo**, pesquise um tipo de erro que você deseja ver de maneira aprofundada. Neste tutorial, vamos utilizar `log_sparkappsexecutors_CL`. Selecione a caixa de seleção correspondente à métrica e, em seguida, clique em **Aplicar**.

    ![Pesquisar erros específicos](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error.png "Pesquisar erros específicos")

        
6. Observe que a consulta na caixa de texto agora muda para uma mostrada na caixa realçada abaixo e os resultados são refinados para mostrar apenas o erro do tipo selecionado.

    ![Pesquisar saída de erros específicos](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-output.png "Pesquisar saída de erros específicos")

7. Agora é possível obter detalhes sobre essa lista de erros específica usando as opções disponíveis no painel esquerdo. Por exemplo, é possível refinar a consulta para examinar apenas mensagens de erro de um nó de trabalho específico.

    ![Pesquisar saída de erros específicos](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "Pesquisar saída de erros específicos")

8. É possível, ainda, se focar na hora em que você acha o erro ocorreu selecionando o valor temporal no painel esquerdo.

    ![Pesquisar saída de erros específicos](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "Pesquisar saída de erros específicos")

9. Agora você está no erro específico que você está procurando. É possível clicar em **[+] mostrar mais** para examinar a mensagem de erro real.

    ![Pesquisar saída de erros específicos](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "Pesquisar saída de erros específicos")

## <a name="create-alerts-to-track-events"></a>Criar alertas para controlar eventos

A primeira etapa para criar um alerta é chegar a uma consulta com base na qual o alerta é disparado. Para simplificar, vamos usar a consulta a seguir que fornece a lista de aplicativos com falha em execução nos clusters HDInsight.

    * (Type=metrics_resourcemanager_queue_root_default_CL) AppsFailed_d>0 

É possível usar qualquer consulta que você desejar para criar um alerta.

1. Abra o painel do OMS. No Portal do Azure, abra a folha de cluster HDInsight associada ao Azure Log Analytics, clique na guia Monitoramento e clique em **Abrir painel do OMS**.

    ![Abrir painel do OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Abrir painel do OMS")

2. No painel do OMS, na tela inicial, clique em **Pesquisa de logs**.

    ![Abrir pesquisa de logs](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Abrir pesquisa de logs")

3. Na janela Pesquisa de logs, na caixa de texto **Iniciar pesquisa aqui**, cole a consulta sobre a qual você deseja criar um alerta, pressione ENTER e, em seguida, clique no botão **Alerta**.

    ![Inserir consulta para criar um alerta](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Inserir consulta para criar um alerta")

4. Na janela **Adicionar regra de alerta**, insira a consulta e outros detalhes para criar um alerta e, em seguida, clique em **Salvar**.

    ![Inserir consulta para criar um alerta](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Inserir consulta para criar um alerta")

    Nesta captura de tela, nós só enviaremos uma notificação por email se a consulta de alerta recuperar uma saída.

5. Também é possível editar ou excluir um alerta existente. Para fazer isso, em qualquer página no portal do OMS, clique no ícone **Configurações**.

    ![Inserir consulta para criar um alerta](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png "Inserir consulta para criar um alerta")

6. Na página **Configurações**, clique em **Alertas** para ver os alertas que você criou. Também é possível habilitar ou desabilitar um alerta, editá-lo ou excluí-lo. Para obter mais informações, consulte [Trabalhando com regras de alerta no Log Analytics](../log-analytics/log-analytics-alerts-creating.md).

## <a name="see-also"></a>Consulte também

* [Trabalhando com o Log Analytics do OMS](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Criar regras de alerta no Log Analytics](../log-analytics/log-analytics-alerts-creating.md)
