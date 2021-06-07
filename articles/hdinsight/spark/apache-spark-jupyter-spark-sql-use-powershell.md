---
title: 'Início Rápido: Criar um cluster Apache Spark no Azure HDInsight com o Azure PowerShell'
description: Este início rápido mostra como usar o Azure PowerShell para criar um cluster Apache Spark no Azure HDInsight e executar uma consultas simples do Spark SQL.
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/12/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 0d4a8df4cb6ba47b7772e2ab3b3247d36622dcb6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865954"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-powershell"></a>Início Rápido: Criar um cluster do Apache Spark no Azure HDInsight usando o PowerShell

Neste início rápido, você usará o Azure PowerShell para criar um cluster Apache Spark no Azure HDInsight. Em seguida, você criará um Jupyter Notebook e o usará para executar consultas SQL do Spark em tabelas do Apache Hive. O Azure HDInsight é um serviço de análise de software livre gerenciado e de amplo espectro para empresas. A estrutura Apache Spark para Azure HDInsight permite análises rápidas de dados e computação de cluster usando o processamento na memória. O Jupyter Notebook permite que você interaja com os seus dados, combine o código com texto Markdown e realizar visualizações simples.

[Visão geral: Apache Spark no Azure HDInsight](apache-spark-overview.md) | [Apache Spark](https://spark.apache.org/) | [Apache Hive](https://hive.apache.org/) | [Jupyter Notebook](https://jupyter.org/)

Se você estiver usando vários clusters juntos, desejará criar uma rede virtual e, se estiver usando um cluster do Spark, também desejará usar o Hive Warehouse Connector. Para obter mais informações, confira [Planejar uma rede virtual para o Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md) e [Integrar o Apache Spark e o Apache Hive com o Hive Warehouse Connector](../interactive-query/apache-hive-warehouse-connector.md).

## <a name="prerequisite"></a>Pré-requisito

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- O [módulo Az do PowerShell](/powershell/azure/install-az-ps).

## <a name="create-an-apache-spark-cluster-in-hdinsight"></a>Criar um cluster Apache Spark no HDInsight

> [!IMPORTANT]  
> A cobrança dos clusters do HDInsight será proporcional por minuto, independentemente de eles estarem sendo usados ou não. Exclua seu cluster depois de terminar de usá-lo. Para saber mais, confira a seção [Recursos de limpeza](#clean-up-resources) deste artigo.

A criação de um cluster HDInsight inclui a criação dos seguintes objetos e recursos do Azure:

- Um grupo de recursos do Azure. Um grupo de recursos do Azure é um contêiner para os recursos do Azure.
- Uma conta de armazenamento do Azure ou um Azure Data Lake Storage.  Cada cluster HDInsight exige um armazenamento de dados dependente. Neste início rápido, você criará um cluster que usa blobs do Armazenamento do Azure como o armazenamento de cluster. Para obter mais informações sobre como usar o Data Lake Storage Gen2, consulte o [Guia de Início Rápido: Configurar clusters no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
- Um cluster de tipos de cluster diferentes no HDInsight.  Neste início rápido, você criará um cluster Spark 2.3.

Você pode usar um script do PowerShell para criar os recursos. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ao executar o script do PowerShell, você precisará inserir os seguintes valores:

|Parâmetro|Valor|
|------|------|
|Nome do grupo de recursos do Azure | Forneça um nome exclusivo para o grupo de recursos.|
|Location| Especifique a região do Azure, por exemplo "EUA Central". |
|Nome da conta de armazenamento padrão | Forneça um nome exclusivo para a conta de armazenamento. |
|Nome do cluster | Forneça um nome exclusivo para o cluster HDInsight.|
|Credenciais de logon de cluster | Use essa conta para se conectar ao painel de cluster posteriormente no início rápido.|
|Credenciais do usuário SSH | Os clientes SSH podem ser usados para criar uma sessão remota de linha de comando com os clusters no HDInsight.|

1. Clique em **Experimentar** no canto superior direito do bloco de código a seguir para abrir o [Azure Cloud Shell](../../cloud-shell/overview.md) e então acompanhe as instruções para se conectar ao Azure.

2. Copie e cole o script do PowerShell a seguir no Cloud Shell.

    ```azurepowershell-interactive
    ### Create a Spark 2.3 cluster in Azure HDInsight

    # Default cluster size (# of worker nodes), version, and type
    $clusterSizeInNodes = "1"
    $clusterVersion = "3.6"
    $clusterType = "Spark"

    # Create the resource group
    $resourceGroupName = Read-Host -Prompt "Enter the resource group name"
    $location = Read-Host -Prompt "Enter the Azure region to create resources in, such as 'Central US'"
    $defaultStorageAccountName = Read-Host -Prompt "Enter the default storage account name"

    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure storage account and container
    # Note: Storage account kind BlobStorage can only be used as secondary storage for HDInsight clusters.
    New-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly 1

    $defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value

    $defaultStorageContext = New-AzStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey

    # Create a Spark 2.3 cluster
    $clusterName = Read-Host -Prompt "Enter the name of the HDInsight cluster"

    # Cluster login is used to secure HTTPS services hosted on the cluster
    $httpCredential = Get-Credential -Message "Enter Cluster login credentials" -UserName "admin"

    # SSH user is used to remotely connect to the cluster using SSH clients
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials" -UserName "sshuser"

    # Set the storage container name to the cluster name
    $defaultBlobContainerName = $clusterName

    # Create a blob container. This holds the default data store for the cluster.
    New-AzStorageContainer `
        -Name $clusterName `
        -Context $defaultStorageContext

    $sparkConfig = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
    $sparkConfig.Add("spark", "2.3")

    # Create the cluster in HDInsight
    New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType $clusterType `
        -OSType "Linux" `
        -Version $clusterVersion `
        -ComponentVersion $sparkConfig `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $clusterName `
        -SshCredential $sshCredentials

    Get-AzHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName
    ```

   Demora cerca de 20 minutos para criar o cluster. O cluster deve ser criado antes de prosseguir para a próxima sessão.

Se você tiver um problema com a criação de clusters HDInsight, talvez não tenha as permissões corretas para fazer isso. Para saber mais, confira [Requisitos do controle de acesso](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="create-a-jupyter-notebook"></a>Criará um Jupyter Notebook

O [Jupyter Notebook](https://jupyter.org/) é um ambiente de notebook interativo que oferece suporte a várias linguagens de programação. O notebook permite que você interaja com seus dados, combine código com texto markdown e execute visualizações simples.

1. No [portal do Azure](https://portal.azure.com), pesquise e selecione **Clusters HDInsight**.
   
   :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-powershell/azure-portal-search-hdinsight-cluster.png" alt-text="A captura de tela mostra a opção Pesquisar por HDInsight do portal do Azure." border="true":::
   
1. Na lista, selecione o cluster que você criou.
   
   :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-powershell/azure-portal-open-hdinsight-cluster.png" alt-text="A captura de tela mostra os clusters do HDInsight com o cluster que você criou." border="true":::
   
1. Na página **Visão geral** do cluster, selecione **Painéis do cluster** e, em seguida, **Jupyter Notebook**. Em caso de solicitação, insira as credenciais de logon do cluster.

   :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png " alt-text="Abrir o Jupyter Notebook para executar consulta SQL interativa do Spark" border="true":::

1. Selecione **Novo** > **PySpark** para criar um notebook.

   :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png " alt-text="Criar um Jupyter Notebook para executar consultas SQL interativas do Spark" border="true":::

   Um novo bloco de anotações é criado e aberto com o nome Untitled(Untitled.pynb).

## <a name="run-apache-spark-sql-statements"></a>Executar instruções SQL do Apache Spark

O SQL (Structured Query Language) é a linguagem mais comum e amplamente usada para consultar e definição de dados. O Spark SQL funciona como uma extensão do Apache Spark para processar dados estruturados, usando a sintaxe SQL familiar.

1. Verifique se o kernel está pronto. O kernel estará pronto quando você vir um círculo vazio ao lado do nome do kernel no notebook. Círculo sólido indica que o kernel está ocupado.

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png " alt-text="Status do kernel" border="true":::

    Quando você inicia o notebook pela primeira vez, o kernel executa algumas tarefas em segundo plano. Aguarde a leitura do kernel. 
1. Cole o código a seguir em uma célula vazia e pressione **SHIFT + ENTER** para executar o código. O comando lista as tabelas de Hive no cluster:

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Quando você usa um Jupyter Notebook com o cluster Spark no HDInsight, obtém uma predefinição `sqlContext` que pode ser usada para executar consultas do Hive com o Spark SQL. `%%sql` informa ao Notebook Jupyter para usar a predefinição `sqlContext` para executar a consulta Hive. A consulta recupera as primeiras 10 linhas de uma tabela Hive (**hivesampletable**) que vem com todos os clusters HDInsight por padrão. Ele leva aproximadamente 30 segundos para obter os resultados. A saída se parece com isso:

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-get-started-hive-query.png " alt-text="Consulta do Apache Hive no Spark no HDInsight" border="true":::

    Toda vez que você executar uma consulta no Jupyter, o título da janela do navegador da Web mostrará um status **(Ocupado)** com o título do bloco de anotações. Você também verá um círculo sólido ao lado do texto **PySpark** no canto superior direito.

1. Execute outra consulta para ver os dados em `hivesampletable`.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    A tela deve ser atualizada para mostrar a saída da consulta.

    :::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-spark-get-started-hive-query-output.png " alt-text="Resultado da consulta do Hive no HDInsight" border="true":::

1. No menu **Arquivo** do notebook, selecione **Fechar e Interromper**. O desligamento do bloco de anotações libera os recursos do cluster.

## <a name="clean-up-resources"></a>Limpar os recursos

O HDInsight salva seus dados no Armazenamento do Azure ou no Azure Data Lake Storage, assim você poderá excluir um cluster quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso. Se você planeja trabalhar no tutorial listado em [Próximas etapas](#next-steps) imediatamente, convém manter o cluster.

Retorne ao Portal do Azure e selecione **Excluir**.

:::image type="content" source="./media/apache-spark-jupyter-spark-sql-use-powershell/hdinsight-azure-portal-delete-cluster.png " alt-text="Excluir um cluster do HDInsight no portal do Azure" border="true":::

Também é possível selecionar o nome do grupo de recursos para abrir a página do grupo de recursos, e depois selecionar **Excluir grupo de recursos**. Ao excluir o grupo de recursos, você exclui o cluster HDInsight e a conta de armazenamento padrão.

### <a name="piecemeal-clean-up-with-powershell-az-module"></a>Limpar por etapas com o módulo Az do PowerShell

```powershell
# Removes the specified HDInsight cluster from the current subscription.
Remove-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName

# Removes the specified storage container.
Remove-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

# Removes a Storage account from Azure.
Remove-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName

# Removes a resource group.
Remove-AzResourceGroup `
    -Name $resourceGroupName
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar um cluster Apache Spark no HDInsight e executar uma consulta SQL básica do Spark. Avance para o próximo tutorial para saber como usar um cluster HDInsight para executar consultas interativas em dados de exemplo.

> [!div class="nextstepaction"]
> [Executar consultas interativas no Apache Spark](./apache-spark-load-data-run-query.md)