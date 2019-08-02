---
title: Gerenciar clusters do Apache Hadoop no HDInsight com o PowerShell - Azure
description: Aprenda a executar tarefas administrativas para os clusters do Apache Hadoop no HDInsight usando o Azure PowerShell.
ms.reviewer: tyfox
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: tyfox
ms.openlocfilehash: 2035f5c7666d93068fd8007555bfe1aadae58ff1
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67873927"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Gerenciar clusters do Apache Hadoop no HDInsight usando o Azure PowerShell
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

O Azure PowerShell pode ser usado para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Neste artigo, você aprenderá a gerenciar clusters de [Apache Hadoop](https://hadoop.apache.org/) no Azure HDInsight usando o módulo Azure PowerShell AZ. Para obter a lista dos cmdlets do PowerShell do HDInsight, consulte a [referência AZ. HDInsight](https://docs.microsoft.com/powershell/module/az.hdinsight).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* O [Módulo Az](https://docs.microsoft.com/powershell/azure/overview) do PowerShell instalado.

## <a name="create-clusters"></a>Criar clusters
Confira [Criar clusters baseados em Linux no HDInsight usando o Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Listar clusters
Use o seguinte comando para listar todos os clusters na assinatura atual:

```powershell
Get-AzHDInsightCluster
```

## <a name="show-cluster"></a>Mostrar cluster
Use o seguinte comando para mostrar os detalhes de um cluster específico na assinatura atual:

```powershell
Get-AzHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Excluir clusters
Use o seguinte comando para excluir um cluster:

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

Você também pode excluir um cluster removendo o grupo de recursos que contém o cluster. Excluir um grupo de recursos excluirá todos os recursos no grupo, incluindo a conta de armazenamento padrão.

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Dimensionar clusters
O recurso de dimensionamento de clusters permite que você altere o número de nós de trabalhador usados por um cluster em execução no Azure HDInsight sem precisar recriar o cluster.

O impacto da alteração do número de nós de dados em cada tipo de cluster com suporte do HDInsight:

* Apache Hadoop

    Você pode aumentar continuamente o número de nós de trabalhador em um cluster Hadoop em execução sem afetar os trabalhos pendentes ou em execução. Novos trabalhos também podem ser enviados enquanto a operação está em andamento. Falhas em uma operação de dimensionamento são normalmente manipuladas para que o cluster sempre seja deixado em um estado funcional.

    Quando um cluster Hadoop é reduzido verticalmente pela diminuição do número de nós de dados, alguns dos serviços no cluster são reiniciados. Restaurar os serviços faz com que todos os trabalhos em execução e pendentes falhem após a conclusão da operação de dimensionamento. Você pode, no entanto, reenviar os trabalhos quando a operação for concluída.
* HBase no Apache

    Você pode adicionar ou remover diretamente nós do cluster HBase enquanto ele é executado. Servidores Regionais são equilibrados automaticamente em alguns minutos após o término da operação de dimensionamento. No entanto, você pode equilibrar manualmente os servidores regionais fazendo logon no nó de cabeçalho do cluster e executando os seguintes comandos em uma janela do prompt de comando:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

* Apache Storm

    Você pode adicionar ou remover nós de dados continuamente para seu cluster Strom enquanto ele é executado. Mas, após a conclusão bem-sucedida da operação de dimensionamento, você precisará redistribuir a topologia.

    A redistribuição pode ser feita de duas maneiras:

  * Interface da Web Storm
  * Ferramenta CLI (interface de linha de comando)

    Consulte a [documentação do Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obter mais detalhes.

    A IU da Web do Storm está disponível no cluster HDInsight:

    ![HDInsight storm dimensionar novo balanceamento](./media/hdinsight-administer-use-powershell/hdinsight.portal.scale.cluster.png)

    Aqui está um exemplo de como usar o comando CLI para reequilibrar a topologia do Storm:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Para alterar o tamanho do cluster Hadoop usando o PowerShell do Azure, execute este comando no computador cliente:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```


## <a name="grantrevoke-access"></a>Conceder/revogar acesso
Os clusters HDInsight têm os seguintes serviços Web HTTP (todos esses serviços têm pontos de extremidade RESTful):

* ODBCODBC
* JDBC
* Ambari
* Oozie
* Templeton

Por padrão, esses serviços são concedidos para acesso. Você pode revogar/conceder o acesso. Para revogar:

```powershell
Revoke-AzHDInsightHttpServicesAccess -ClusterName <Cluster Name>
```

Para conceder:

```powershell
$clusterName = "<HDInsight Cluster Name>"

# Credential option 1
$hadoopUserName = "admin"
$hadoopUserPassword = "<Enter the Password>"
$hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

# Credential option 2
#$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Grant-AzHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential
```

> [!NOTE]  
> Ao conceder/revogar o acesso, você redefinirá o nome de usuário de cluster e a senha.

As ações de conceder e revogar o acesso também podem ser feitas pelo portal. Consulte [gerenciar clusters de Apache Hadoop no HDInsight usando o portal do Azure](hdinsight-administer-use-portal-linux.md).

## <a name="update-http-user-credentials"></a>Atualizar credenciais de usuário HTTP
É o mesmo procedimento que Conceder/revogar acesso HTTP. Se o cluster recebeu o acesso HTTP, você deverá, primeiramente, revogá-lo.  E, em seguida, conceder acesso com novas credenciais de usuário HTTP.

## <a name="find-the-default-storage-account"></a>Encontrar a conta de armazenamento padrão
O script do PowerShell a seguir demonstra como obter o nome da conta de armazenamento padrão e as informações relacionadas:

```powershell
#Connect-AzAccount
$clusterName = "<HDInsight Cluster Name>"

$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$storageInfo = $clusterInfo.DefaultStorageAccount.split('.')
$defaultStoreageType = $storageInfo[1]
$defaultStorageName = $storageInfo[0]

echo "Default Storage account name: $defaultStorageName"
echo "Default Storage account type: $defaultStoreageType"

if ($defaultStoreageType -eq "blob")
{
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey

    echo "Default Blob container name: $defaultBlobContainerName"
    echo "Default Storage account key: $defaultStorageAccountKey"
}
```


## <a name="find-the-resource-group"></a>Encontrar o grupo de recursos
No modo do Gerenciador de Recursos, cada cluster HDInsight pertence a um grupo de recursos do Azure.  Encontrar o grupo de recursos:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```


## <a name="submit-jobs"></a>Enviar trabalhos
**Enviar trabalhos MapReduce**

Veja [Execute os exemplos MapReduce incluídos no HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Para enviar trabalhos do Apache Hive**

Consulte [Executar consultas do Apache Hive usando o PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).

**Para enviar trabalhos do Apache Sqoop**

Veja [Use o Apache Sqoop com o HDInsight](hadoop/hdinsight-use-sqoop.md).

**Para enviar trabalhos do Apache Oozie**

Consulte [Use o Apache Oozie com o Apache Hadoop para definir e executar um fluxo de trabalho no HDInsight](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Carregar dados no armazenamento de Blob do Azure

Consulte [carregar dados no HDInsight](hdinsight-upload-data.md).

## <a name="see-also"></a>Consulte também

* [Documentação de referência do cmdlet do HDInsight](https://msdn.microsoft.com/library/azure/dn479228.aspx)
* [Gerenciar clusters do Apache Hadoop no HDInsight usando o portal do Azure](hdinsight-administer-use-portal-linux.md)
* [Administrar o HDInsight usando uma interface de linha de comando](hdinsight-administer-use-command-line.md)
* [Criar clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Enviar trabalhos do Apache Hadoop de forma programática](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Introdução ao Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
