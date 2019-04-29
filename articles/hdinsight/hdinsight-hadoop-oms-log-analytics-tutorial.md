---
title: Use o Azure Monitor registra em log para monitorar clusters Azure HDInsight
description: Saiba como usar os logs do Azure do Azure Monitor para monitorar trabalhos em execução em um cluster HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: hrasheed
ms.openlocfilehash: 0c37ff3c62817d238a94d5bbeae90c67663a6029
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63761203"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Use o Azure Monitor registra em log para monitorar clusters do HDInsight

Saiba como habilitar os logs do Azure Monitor monitorar as operações de cluster Hadoop no HDInsight e como adicionar uma solução de monitoramento de HDInsight.

[Os logs do Azure Monitor](../log-analytics/log-analytics-overview.md) é um serviço no Azure Monitor que monitora a sua nuvem e ambientes para manter a disponibilidade e desempenho no local. Ele coleta dados gerados pelos recursos em seus ambientes de nuvem e locais e de outras ferramentas de monitoramento para fornecer análise de várias fontes.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* **Um workspace do Log Analytics**. Você pode pensar neste espaço de trabalho como um ambiente de logs do Azure Monitor exclusivo com seu próprio repositório de dados, fontes de dados e soluções. Para obter instruções, consulte [Criar um espaço de trabalho do Log Analytics](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* **Um cluster Azure HDInsight**. No momento, você pode usar os logs do Azure Monitor com os seguintes tipos de cluster do HDInsight:

  * O Hadoop
  * HBase
  * Consulta Interativa
  * Kafka
  * Spark
  * Storm

  Para obter instruções sobre como criar um cluster HDInsight, consulte [Introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* **Módulo do Azure PowerShell Az**.  Ver [apresentando o novo módulo do PowerShell do Azure Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

> [!NOTE]  
> É recomendável colocar o cluster HDInsight e o espaço de trabalho do Log Analytics na mesma região para melhorar o desempenho. Os logs do Azure Monitor não está disponível em todas as regiões do Azure.

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>Habilitar os logs do Azure Monitor por meio do portal

Nesta seção, você configurará um cluster HDInsight Hadoop existente para usar um espaço de trabalho do Azure Log Analytics para monitorar trabalhos, logs de depuração, etc.

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No menu esquerdo, selecione **Todos os serviços**.

3. Em **ANÁLISES**, selecione **Clusters do HDInsight**.

4. Selecione o cluster na lista.

5. Da esquerda, sob **Monitoramento**, selecione **Conjunto de Gerenciamento de Operações**.

6. Na exibição principal, sob **Monitoramento de OMS**, selecione **Habilitar**.

7. Na lista suspensa **Selecionar espaço de trabalho**, selecione um espaço de trabalho do Log Analytics existente.

8. Clique em **Salvar**.  Levará alguns minutos para salvar a configuração.

    ![Habilitar o monitoramento para clusters HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-enable-monitoring.png "Habilitar o monitoramento para clusters HDInsight")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Habilitar os logs do Azure Monitor por meio do PowerShell do Azure

Você pode habilitar os logs do Azure Monitor usando o módulo Azure PowerShell Az [Enable-AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightoperationsmanagementsuite) cmdlet.

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace -ResourceGroupName $resourceGroup -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace -ResourceGroupName $resourceGroup -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables Operations Management Suite
Enable-AzHDInsightOperationsManagementSuite -ResourceGroupName $resourceGroup -Name $cluster -WorkspaceId $WorkspaceId -PrimaryKey $PrimaryKey
```

Para desabilitar o uso de [Disable-AzHDInsightOperationsManagementSuite](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightoperationsmanagementsuite) cmdlet:

```powershell
Disable-AzHDInsightOperationsManagementSuite -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Instalar solução de gerenciamento do cluster do Microsoft Azure HDInsight

HDInsight fornece soluções de gerenciamento específicas para cluster que você pode adicionar para logs do Azure Monitor. [Soluções de gerenciamento](../log-analytics/log-analytics-add-solutions.md) adicionar funcionalidade para os logs do Azure Monitor, fornecendo dados adicionais e ferramentas de análise. Essas soluções coletam métricas de desempenho importantes de seus clusters HDInsight e fornecem as ferramentas para pesquisá-las. Essas soluções também fornecem visualizações e painéis para a maioria dos tipos de cluster com suporte no HDInsight. Usando as métricas que coleta com a solução, você pode criar alertas e regras de monitoramentos personalizadas.

Estas são as soluções de Microsoft Azure HDInsight disponíveis:

* Monitoramento do Hadoop no HDInsight
* Monitoramento do HDInsight HBase
* Monitoramento de Consulta Interativo do Microsoft Azure HDInsight
* Monitoramento do Kafka no HDInsight
* Monitoramento do Spark no HDInsight
* Monitoramento do Storm no HDInsight

Para obter instruções para instalar uma solução de gerenciamento, consulte [Soluções de Gerenciamento do Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Para fazer experiências, instale uma solução de monitoramento do HDInsight Hadoop. Quando estiver pronto, você verá uma peça **HDInsightHadoop** listada em **Resumo**. Selecione a peça **HDInsightHadoop**. A solução HDInsightHadoop é semelhante a:

![Modo de exibição de solução de monitoramento do HDInsight](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Como o cluster é uma marca nova, o relatório não mostra todas as atividades.

## <a name="next-steps"></a>Próximas etapas

* [Logs de consulta do Azure Monitor para monitorar clusters do HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
