---
title: Uso de logs do Azure Monitor para monitorar clusters do Azure HDInsight
description: Saiba como usar os logs do Azure Monitor para monitorar trabalhos sendo executados em um cluster HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-azurecli, devx-track-azurepowershell
ms.date: 05/13/2020
ms.openlocfilehash: 5f849248021de3c17b69517d214f6f3fd6e4ee3a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945631"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Uso de logs do Azure Monitor para monitorar clusters do HDInsight

Saiba como usar os logs do Azure Monitor para monitorar operações de cluster Hadoop em HDInsight. E como adicionar uma solução de monitoramento do HDInsight.

Os [logs do Azure Monitor](../azure-monitor/log-query/log-query-overview.md) representam um serviço do Azure Monitor que monitora os ambientes de nuvem e locais. O objetivo do monitoramento é manter a disponibilidade e o desempenho. Ele coleta dados gerados pelos recursos em seus ambientes de nuvem e locais e de outras ferramentas de monitoramento. Os dados são usados para fornecer análise para várias fontes.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um espaço de trabalho do Log Analytics. É possível pensar neste workspace como um ambiente exclusivo de logs do Azure Monitor com seu próprio repositório de dados, fontes de dados e soluções. Para obter instruções, consulte [Criar um espaço de trabalho do Log Analytics](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* Um cluster Azure HDInsight. No momento, é possível usar os logs do Azure Monitor com os seguintes tipos de cluster HDInsight:

  * O Hadoop
  * HBase
  * Consulta Interativa
  * Kafka
  * Spark
  * Storm

  Para obter instruções sobre como criar um cluster HDInsight, consulte [Introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* Se estiver usando o PowerShell, você precisará do [Az Module](/powershell/azure/). Verifique se você tem a versão mais recente. Se for necessário, execute `Update-Module -Name Az`.

* Se desejar usar a CLI do Azure e você ainda não a instalou, veja [Instalação da CLI do Azure](/cli/azure/install-azure-cli).

> [!NOTE]  
> É recomendável colocar o cluster HDInsight e o espaço de trabalho do Log Analytics na mesma região para melhorar o desempenho. O serviço de logs do Azure Monitor não está disponível em todas as regiões do Azure.

## <a name="enable-azure-monitor-using-the-portal"></a>Habilitar o Azure Monitor usando o portal

Nesta seção, você configurará um cluster HDInsight Hadoop existente para usar um workspace do Log Analytics para monitorar trabalhos, logs de depuração, e assim por diante.

1. No [portal do Azure](https://portal.azure.com/), selecione o cluster. O cluster abre em uma nova página do portal.

1. No lado esquerdo, em **Monitoramento**, selecione **Azure Monitor**.

1. No modo de exibição principal, em **Integração do Azure Monitor**, selecione **Habilitar**.

1. Na lista suspensa **Selecionar espaço de trabalho**, selecione um espaço de trabalho do Log Analytics existente.

1. Clique em **Salvar**.  Levará alguns minutos para salvar a configuração.

    ![Habilitar monitoramento para clusters do HDInsight](./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png "Habilitar monitoramento para clusters do HDInsight")

## <a name="enable-azure-monitor-using-azure-powershell"></a>Habilitar o Azure Monitor usando o Azure PowerShell

Você pode habilitar os logs do Azure Monitor usando o cmdlet [Enable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) do módulo Az do Azure PowerShell.

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster
```

Para desabilitar, use o cmdlet [Disable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring):

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="enable-azure-monitor-using-azure-cli"></a>Habilitar o Azure Monitor usando a CLI do Azure

Você pode habilitar logs de Azure Monitor usando o `[az hdinsight monitor enable` comando CLI do Azure] (/CLI/Azure/hdinsight/monitor # AZ-hdinsight-monitor-enable).

```azurecli
# set variables
export resourceGroup=RESOURCEGROUPNAME
export cluster=CLUSTERNAME
export LAW=LOGANALYTICSWORKSPACENAME

# Enable the Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor enable --name $cluster --resource-group $resourceGroup --workspace $LAW

# Get the status of Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor show --name $cluster --resource-group $resourceGroup
```

Para desabilitar, use o comando [`az hdinsight monitor disable`](/cli/azure/hdinsight/monitor#az-hdinsight-monitor-disable).

```azurecli
az hdinsight monitor disable --name $cluster --resource-group $resourceGroup
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Instalar solução de gerenciamento do cluster do Microsoft Azure HDInsight

O HDInsight fornece soluções de gerenciamento específicas para cluster que podem ser adicionadas aos logs do Azure Monitor. As [soluções de gerenciamento](../azure-monitor/insights/solutions.md) adicionam funcionalidade aos logs do Azure Monitor, fornecendo dados adicionais e ferramentas de análise. Essas soluções coletam métricas de desempenho importantes de seus clusters HDInsight. E fornecem as ferramentas para pesquisar as métricas. Essas soluções também fornecem visualizações e painéis para a maioria dos tipos de cluster com suporte no HDInsight. Usando as métricas que coleta com a solução, você pode criar alertas e regras de monitoramentos personalizadas.

Soluções de HDInsight disponíveis:

* Monitoramento do Hadoop no HDInsight
* Monitoramento do HDInsight HBase
* Monitoramento de Consulta Interativo do Microsoft Azure HDInsight
* Monitoramento do Kafka no HDInsight
* Monitoramento do Spark no HDInsight
* Monitoramento do Storm no HDInsight

Para obter instruções sobre uma solução de gerenciamento, veja [Soluções de gerenciamento no Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Para fazer um experimento, instale uma solução de monitoramento do HDInsight Hadoop. Quando estiver pronto, você verá um bloco **HDInsightHadoop** listado em **Resumo**. Selecione a peça **HDInsightHadoop**. A solução HDInsightHadoop é semelhante a:

![Modo de exibição de solução de monitoramento do HDInsight](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Como o cluster é uma marca nova, o relatório não mostra todas as atividades.

## <a name="configuring-performance-counters"></a>Configuração dos contadores de desempenho

O Azure Monitor dá suporte à coleta e análise de métricos de desempenho para os nós no cluster. Para obter mais informações, veja [Fontes de dados de desempenho do Linux no Azure Monitor](../azure-monitor/platform/data-sources-performance-counters.md#linux-performance-counters).

## <a name="cluster-auditing"></a>Auditoria de cluster

O HDInsight dá suporte à auditoria de cluster com os logs do Azure Monitor, importando os seguintes tipos de logs:

* `log_gateway_audit_CL` - esta tabela fornece logs de auditoria de nós de gateway do cluster que mostram tentativas de logon bem-sucedidas e com falha.
* `log_auth_CL` - esta tabela fornece logs do protocolo SSH com tentativas de logon bem-sucedidas e com falha.
* `log_ambari_audit_CL` - esta tabela fornece logs de auditoria do Ambari.
* `log_ranger_audti_CL` - esta tabela fornece logs de auditoria do Apache Ranger em clusters ESP.

## <a name="next-steps"></a>Próximas etapas

* [Consulta logs do Azure Monitor para monitorar clusters do HDInsight](hdinsight-hadoop-oms-log-analytics-use-queries.md)
* [Como monitorar a disponibilidade do cluster com os logs do Apache Ambari e do Azure Monitor](./hdinsight-cluster-availability.md)