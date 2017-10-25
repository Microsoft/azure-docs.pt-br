---
title: Usar o Log Analytics para monitorar clusters HDInsight do Azure | Microsoft Docs
description: Saiba como usar o Azure Log Analytics para monitorar trabalhos sendo executados em um cluster HDInsight.
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
ms.openlocfilehash: c1c74c797449c2fa6d76438f9ec33eaf0fe51af4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-log-analytics-to-monitor-hdinsight-clusters-preview"></a>Usar o Log Analytics do Azure para monitorar clusters HDInsight (versão prévia)

Neste artigo, você saberá como usar o Azure Log Analytics para monitorar as operações de clusters HDInsight do Hadoop.

O Log Analytics é um serviço no [OMS (Operations Management Suite)](../operations-management-suite/operations-management-suite-overview.md) que monitora seus ambientes na nuvem e locais a fim de manter a disponibilidade e o desempenho. Ele coleta dados gerados pelos recursos em seus ambientes de nuvem e locais e de outras ferramentas de monitoramento para fornecer análise de várias fontes. 

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure**. Antes de começar este tutorial, você deverá ter uma assinatura do Azure. Consulte [Criar sua conta gratuita do Azure hoje](https://azure.microsoft.com/free).

* **Um cluster Azure HDInsight**. No momento, é possível usar o Azure OMS com os seguintes tipos de cluster HDInsight:
    * O Hadoop
    * Spark
    * HBase
    * Storm
    * Kafka
    * Hive Interativo

    Para obter instruções sobre como criar um cluster HDInsight, consulte [Introdução ao Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).


* **Um espaço de trabalho do Log Analytics**. É possível pensar neste espaço de trabalho como um ambiente exclusivo do Log Analytics com seu próprio repositório de dados, fontes de dados e soluções. É necessário ter um desse espaço de trabalho já criado que você pode associar aos clusters do Azure HDInsight. Para obter instruções, consulte [Criar um espaço de trabalho do Log Analytics](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace).

## <a name="configure-hdinsight-cluster-to-use-azure-log-analytics"></a>Configurar o cluster HDInsight para usar o Azure Log Analytics

Nesta seção, você configurará um cluster HDInsight Hadoop existente para usar um espaço de trabalho do Azure Log Analytics para monitorar trabalhos, logs de depuração, etc.

1. No Portal do Azure, no painel esquerdo, clique em **clusters HDInsight** e, em seguida, clique no nome do cluster que você deseja configurar com o Azure Log Analytics.

2. Na folha do cluster, no painel esquerdo, clique em **Monitoramento**.

3. No painel direito, clique em **Habilitar** e, em seguida, selecione um espaço de trabalho do Log Analytics existente. Clique em **Salvar**.

    ![Habilitar o monitoramento para clusters HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "Habilitar o monitoramento para clusters HDInsight")

4. Depois que o cluster for configurado para usar o Log Analytics para monitoramento, você verá uma opção **Abrir painel do OMS** na parte superior da guia. Clique no botão.

    ![Abrir painel do OMS](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-open-workspace.png "Abrir painel do OMS")

5. Insira suas credenciais do Azure, se solicitado. Você é direcionado ao painel do Microsoft OMS.

    ![Portal do Operations Management Suite](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring-oms-portal.png "Portal do Operations Management Suite")

## <a name="next-steps"></a>Próximas etapas
* [Adicionar soluções de gerenciamento de cluster HDInsight para o Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md)
