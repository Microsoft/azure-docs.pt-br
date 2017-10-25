---
title: "Adicionar soluções de gerenciamento de cluster HDInsight ao Log Analytics do Azure | Microsoft Docs"
description: "Saiba como usar o Azure Log Analytics para criar exibições personalizadas para clusters HDInsight."
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
ms.date: 09/12/2017
ms.author: nitinme
ms.openlocfilehash: 21b474e37ef0a6037e05ee1fe8e5088cb3e3601d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="add-hdinsight-cluster-management-solutions-to-log-analytics-preview"></a>Adicionar soluções de gerenciamento de cluster HDInsight ao Log Analytics (versão prévia)

O HDInsight fornece soluções de gerenciamento específicas para cluster que podem ser adicionadas para o Azure Log Analytics. [As soluções de gerenciamento](../log-analytics/log-analytics-add-solutions.md) adicionam funcionalidade ao OMS, fornecendo dados adicionais e ferramentas de análise ao Log Analytics. Essas soluções coletam métricas de desempenho importantes de seus clusters HDInsight e fornecem as ferramentas para pesquisá-las. Essas soluções também fornecem visualizações e painéis para a maioria dos tipos de cluster com suporte no HDInsight. Usando as métricas que coleta com a solução, você pode criar alertas e regras de monitoramentos personalizadas. 

Neste artigo, você aprenderá a adicionar soluções de gerenciamento específicas para cluster a um espaço de trabalho do OMS.

## <a name="prerequisites"></a>Pré-requisitos

* É necessário ter configurado um cluster HDInsight para usar o Azure Log Analytics. Para obter instruções, consulte [Use Azure Log Analytics with HDInsight clusters](hdinsight-hadoop-oms-log-analytics-tutorial.md) (Usar o Azure Log Analytics com clusters HDInsight).

## <a name="add-cluster-specific-management-solutions"></a>Adicionar soluções de gerenciamento específicas para cluster

Nesta seção, você adicionará uma solução de gerenciamento de cluster HBase a um espaço de trabalho existente do OMS. Soluções semelhantes para outros tipos de cluster HDInsight estarão disponíveis em breve.

1. Abra o painel do OMS. No Portal do Azure, abra a folha de cluster HDInsight associada ao Azure Log Analytics, clique na guia Monitoramento e clique em **Abrir painel do OMS**.

    ![Abrir painel do OMS](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-log-analytics-open-oms-dashboard.png "Abrir painel do OMS")

1. No painel do OMS, clique em **Galeria de soluções** ou no ícone do Designer de exibição no painel esquerdo.

    ![Adicionar solução de gerenciamento no OMS](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-add-management-solution-oms-portal.png "Adicionar solução de gerenciamento no OMS")

2. Na Galeria de soluções, localize **Monitoramento do HBase no HDInsight** e, em seguida, clique no bloco.

    ![Localizar a solução de gerenciamento do HBase](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/find-hbase-management-solution.png "Localizar a solução de gerenciamento do HBase")

3. Na próxima tela, clique em **Adicionar**.

     ![Adicionar a solução de gerenciamento do HBase](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/add-hbase-management-solution.png "Adicionar a solução de gerenciamento do HBase")

4. Agora você deve ver um bloco no painel do OMS para a solução de gerenciamento do HBase. Se o cluster associado ao OMS (como parte do pré-requisito para este artigo) for um cluster HBase, o bloco mostrará o nome do cluster e o número de nós nele.

    ![Solução de gerenciamento do HBase adicionada](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/added-hbase-management-solution.png "Solução de gerenciamento do HBase adicionada")

## <a name="next-steps"></a>Próximas etapas

* [Consultar o Azure Log Analytics ara monitorar clusters HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)

## <a name="see-also"></a>Consulte também

* [Trabalhando com o Log Analytics do OMS](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Criar regras de alerta no Log Analytics](../log-analytics/log-analytics-alerts-creating.md)
