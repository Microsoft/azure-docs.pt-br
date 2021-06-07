---
title: Personalizar as configurações de cluster do Azure HDInsight usando a inicialização
description: Saiba como personalizar a configuração do cluster HDInsight programaticamente usando modelos .NET, PowerShell e Resource Manager.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/01/2020
ms.openlocfilehash: 55792cc1890702b904db00b7e3cb3ac3a78c63f1
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866515"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Personalizar clusters do HDInsight usando a Inicialização

Os scripts de Bootstrap permitem que você instale e configure componentes no Azure HDInsight programaticamente.

Há três abordagens para definir as configurações do arquivo de configuração à medida que seu cluster HDInsight é criado:

* Usar PowerShell do Azure
* Usar o SDK do .NET
* Usar o modelo do Azure Resource Manager

Por exemplo, usando esses métodos programáticos, você pode configurar opções nesses arquivos:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* server.properties (configuração do kafka broker)

Para obter informações sobre como instalar componentes adicionais no cluster HDInsight durante o tempo de criação, consulte [Personalizar clusters hdinsight usando a ação de script (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="prerequisites"></a>Pré-requisitos

* Se estiver usando o PowerShell, você precisará do [Az Module](/powershell/azure/).

## <a name="use-azure-powershell"></a>Usar PowerShell do Azure

O seguinte código do PowerShell personaliza uma configuração de [Apache Hive](https://hive.apache.org/):

> [!IMPORTANT]  
> O parâmetro `Spark2Defaults` pode precisar ser usado com [Add-AzHDInsightConfigValue](/powershell/module/az.hdinsight/add-azhdinsightconfigvalue). Você pode passar valores vazios para o parâmetro, conforme mostrado no exemplo de código abaixo.

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90s" }

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

New-AzHDInsightCluster `
    -ResourceGroupName $existingResourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -Config $config
```

Um script do PowerShell completo em funcionamento pode ser encontrado no [Apêndice](#appendix-powershell-sample).

**Para verificar a alteração:**

1. Navegue até `https://CLUSTERNAME.azurehdinsight.net/` onde `CLUSTERNAME` é o nome do cluster.
1. No menu à esquerda, navegue até configurações do **Hive**  >    >  **avançado**.
1. Expanda **avançado Hive-site**.
1. Localize **Hive. metastore. Client. Socket. Timeout** e confirme se o valor é **anos 90**.

Alguns outros exemplos de personalização de outros arquivos de configuração:

```xml
# hdfs-site.xml configuration
$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

# core-site.xml configuration
$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

# mapred-site.xml configuration
$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

# oozie-site.xml configuration
$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
```

## <a name="use-net-sdk"></a>Usar o SDK do .NET

Consulte [SDK do Azure HDInsight para .net](/dotnet/api/overview/azure/hdinsight).

## <a name="use-resource-manager-template"></a>Usar modelo do Resource Manager

Você pode usar o bootstrap no modelo do Resource Manager:

```json
"configurations": {
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

:::image type="content" source="./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png" alt-text="O Hadoop personaliza o modelo de Azure Resource Manager de inicialização de cluster":::

Exemplo de trecho do modelo do Resource Manager para alternar a configuração em spark2-padrões para limpar periodicamente os logs de eventos do armazenamento.  

```json
"configurations": {
    "spark2-defaults": {
        "spark.history.fs.cleaner.enabled": "true",
        "spark.history.fs.cleaner.interval": "7d",
        "spark.history.fs.cleaner.maxAge": "90d"
    }
}
```

## <a name="see-also"></a>Confira também

* [Criar clusters Apache Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md) fornece instruções sobre como criar um cluster HDInsight usando outras opções personalizadas.
* [Desenvolver scripts de ação de script para o HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Instalar e usar o Apache Spark em clusters HDInsight](spark/apache-spark-jupyter-spark-sql-use-portal.md)
* [Instale e use o Apache o giraph em clusters HDInsight](./hdinsight-hadoop-hue-linux.md).

## <a name="appendix-powershell-sample"></a>Apêndice: amostra do PowerShell

Esse script do PowerShell cria um cluster HDInsight e personaliza uma configuração de Hive. Certifique-se de inserir valores para `$nameToken` , `$httpPassword` e `$sshPassword` .

```powershell
####################################
# Set these variables
####################################
#region - used for creating Azure service names
$nameToken = "<ENTER AN ALIAS>"
#endregion

#region - cluster user accounts
$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = '<ENTER A PASSWORD>'

$sshUserName = "sshuser" #HDInsight ssh user name
$sshPassword = '<ENTER A PASSWORD>'
#endregion

####################################
# Service names and varialbes
####################################
#region - service names
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

$resourceGroupName = $namePrefix + "rg"
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName

$location = "East US"
#endregion


####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

#region - Create an HDInsight cluster
####################################
# Create dependent components
####################################
Write-Host "Creating a resource group ..." -ForegroundColor Green
New-AzResourceGroup `
    -Name  $resourceGroupName `
    -Location $location

Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

# Note: Storage account kind BlobStorage cannot be used as primary storage.

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

New-AzStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageContext #use the cluster name as the container name

####################################
# Create a configuration object
####################################
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90s"}

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

####################################
# Create an HDInsight cluster
####################################
$httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

$sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
$sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

New-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -Location $location `
    -ClusterSizeInNodes 1 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -Config $config

####################################
# Verify the cluster
####################################
Get-AzHDInsightCluster `
    -ClusterName $hdinsightClusterName

#endregion
```