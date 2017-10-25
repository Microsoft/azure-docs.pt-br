---
title: Transformar dados usando o Spark no Azure Data Factory | Microsoft Docs
description: "Este tutorial fornece instruções passo a passo para transformar dados usando a Atividade Spark no Azure Data Factory."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/10/2017
ms.author: shengc
ms.openlocfilehash: 93031615b271e542d8832b980a40ca25d1cd6d5c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="transform-data-in-the-cloud-by-using-spark-activity-in-azure-data-factory"></a>Transformar os dados na nuvem usando a atividade Spark no Azure Data Factory
O Azure Data Factory é um serviço de integração de dados baseado em nuvem que permite que você crie fluxos de trabalho controlados por dados na nuvem para orquestrar e automatizar a movimentação e a transformação de dados. Usando o Azure Data Factory, você pode criar e agendar fluxos de trabalho orientados a dados (chamados de pipelines) que podem ingerir dados de repositórios de dados diferentes, processar/transformr os dados usando serviços de computação como o Hadoop do Azure HDInsight, Spark, Azure Data Lake Analytics e Azure Machine Learning e publicar os dados de saída em repositórios de dados como o SQL Data Warehouse do Azure para consumo pelos aplicativos de business intelligence (BI). 

Neste tutorial, você pode usar o Azure PowerShell para criar um pipeline do Data Factory que transforma dados usando a Atividade Spark e um serviço vinculado HDInsight sob demanda. Neste tutorial, você executa as seguintes etapas:

> [!div class="checklist"]
> * Criar uma fábrica de dados. 
> * Criar e implantar serviços vinculados.
> * Criar e implantar um pipeline. 
> * Inicie uma execução de pipeline.
> * Monitore a execução de pipeline.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
* **Conta de Armazenamento do Azure**. Você cria um script Python e um arquivo de entrada e carrega-os no Armazenamento do Azure. A saída do programa Spark é armazenada nessa conta de armazenamento. O cluster do Spark sob demanda usa a mesma conta de armazenamento que o respectivo armazenamento primário.  
* **PowerShell do Azure**. Siga as instruções em [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-azurerm-ps).


### <a name="upload-python-script-to-your-blob-storage-account"></a>Carregar o script Python em sua conta de Armazenamento de Blobs
1. Crie um arquivo de Python chamado **WordCount_Spark.py** com o seguinte conteúdo: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Substitua **&lt;storageAccountName&gt;** pelo nome da sua conta de Armazenamento do Azure. Em seguida, salve o arquivo. 
3. No seu Armazenamento de Blobs do Azure, crie um contêiner denominado **adftutorial** se ele não existir. 
4. Crie uma pasta chamada **spark**.
5. Criar uma subpasta chamada **script** na pasta **spark**. 
6. Carregue o arquivo **WordCount_Spark.py** na subpasta **script**. 


### <a name="upload-the-input-file"></a>Carregue o arquivo de entrada
1. Crie um arquivo chamado **minecraftstory.txt** com um pouco de texto. O programa Spark conta o número de palavras no texto. 
2. Criar uma subpasta chamada `inputfiles` na pasta `spark`. 
3. Carregue o `minecraftstory.txt` na subpasta `inputfiles`. 

## <a name="author-linked-services"></a>Criar serviços vinculados
Você cria dois serviços vinculados nesta seção: 
    
- Um serviço vinculado do Armazenamento do Azure que vincula uma conta de Armazenamento do Azure ao data factory. Esse armazenamento é usado pelo cluster HDInsight sob demanda. Ele também contém o script Spark a ser executado. 
- Um serviço vinculado do HDInsight sob demanda. O Azure Data Factory cria automaticamente um cluster HDInsight, executa o programa Spark e então exclui o cluster HDInsight depois de ele ficar ocioso por um tempo pré-configurado. 

### <a name="azure-storage-linked-service"></a>Serviço vinculado de armazenamento do Azure
Crie um arquivo JSON usando seu editor preferido, copie a seguinte definição de JSON de um serviço vinculado do Armazenamento do Azure e, em seguida, salve o arquivo como **MyStorageLinkedService.json**.  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": {
          "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
          "type": "SecureString"
        }
      }
    }
}
```
Atualize o &lt;storageAccountName&gt; e o &lt;storageAccountKey&gt; com o nome e a chave de sua conta de Armazenamento do Azure. 


### <a name="on-demand-hdinsight-linked-service"></a>Serviço vinculado do HDInsight sob demanda
Crie um arquivo JSON usando seu editor preferido, copie a seguinte definição de JSON de um serviço vinculado do Azure HDInsight e, em seguida, salve o arquivo como **MyOnDemandSparkLinkedService.json**.  

```json
{
    "name": "MyOnDemandSparkLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
        "clusterSize": 2,
        "clusterType": "spark",
        "timeToLive": "00:15:00",
        "hostSubscriptionId": "<subscriptionID> ",
        "servicePrincipalId": "<servicePrincipalID>",
        "servicePrincipalKey": {
          "value": "<servicePrincipalKey>",
          "type": "SecureString"
        },
        "tenant": "<tenant ID>",
        "clusterResourceGroup": "<resourceGroupofHDICluster>",
        "version": "3.6",
        "osType": "Linux",
        "clusterNamePrefix":"ADFSparkSample",
        "linkedServiceName": {
          "referenceName": "MyStorageLinkedService",
          "type": "LinkedServiceReference"
        }
      }
    }
}
```
Atualize os valores para as propriedades a seguir na definição de serviço vinculado: 

- **hostSubscriptionId**. Substitua &lt;subscriptionID&gt; pela ID da assinatura do Azure. O cluster HDInsight sob demanda é criado nessa assinatura. 
- **locatário**. Substitua &lt;tenantID&gt; pela ID do locatário do Azure. 
- **servicePrincipalId**, **servicePrincipalKey**. Substitua &lt;servicePrincipalID&gt; e &lt;servicePrincipalKey&gt; pela ID e a chave da entidade de serviço no Azure Active Directory. Essa entidade de serviço precisa ser um membro da função de Colaborador de assinatura ou o grupo de recursos em que o cluster é criado. Veja [criar o aplicativo do Azure Active Directory e a entidade de serviço](../azure-resource-manager/resource-group-create-service-principal-portal.md) para obter detalhes. 
- **clusterResourceGroup**. Substitua &lt;resourceGroupOfHDICluster&gt; pelo nome do grupo de recursos no qual o cluster HDInsight precisa ser criado. 

> [!NOTE]
> O Azure HDInsight tem uma limitação para o número total de núcleos que você pode usar em cada região do Azure a que ele dá suporte. Para o serviço vinculado do HDInsight sob demanda, o cluster HDInsight será criado na mesma localização do Armazenamento do Azure usado como o armazenamento primário desse serviço vinculado. Verifique se você tem cotas de núcleo suficientes para que o cluster seja criado com êxito. Para obter mais informações, consulte [Configurar clusters no HDInsight com Hadoop, Spark, Kafka e mais](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). 


## <a name="author-a-pipeline"></a>Criar um pipeline 
Nesta etapa, você cria um pipeline com uma atividade Spark. A atividade usa a amostra de **contagem de palavras**. Baixe o conteúdo dessa localização, se você ainda não tiver feito isso.

Crie um arquivo JSON em seu editor preferido, copie a definição de JSON a seguir de uma definição de pipeline e salve-a como **MySparkOnDemandPipeline.json**. 

```json
{
  "name": "MySparkOnDemandPipeline",
  "properties": {
    "activities": [
      {
        "name": "MySparkActivity",
        "type": "HDInsightSpark",
        "linkedServiceName": {
            "referenceName": "MyOnDemandSparkLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "rootPath": "adftutorial/spark",
          "entryFilePath": "script/WordCount_Spark.py",
          "getDebugInfo": "Failure",
          "sparkJobLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }
    ]
  }
}
```

Observe os seguintes pontos: 

- rootPath aponta para a pasta spark do contêiner adftutorial. 
- entryFilePath aponta para o arquivo WordCount_Spark.py na subpasta script da pasta spark. 


## <a name="create-a-data-factory"></a>Criar uma data factory 
Você criou definições de serviço vinculado e de pipeline em arquivos JSON. Agora, criaremos um data factory e implantaremos o serviço vinculado e arquivos JSON de pipeline usando cmdlets do PowerShell. Execute os seguintes comandos do PowerShell, um de cada vez: 

1. Defina as variáveis uma a uma.

    ```powershell
    $subscriptionID = "<subscription ID>" # Your Azure subscription ID
    $resourceGroupName = "ADFTutorialResourceGroup" # Name of the resource group
    $dataFactoryName = "MyDataFactory09102017" # Globally unique name of the data factory
    $pipelineName = "MySparkOnDemandPipeline" # Name of the pipeline
    ```
2. Inicie o **PowerShell**. Mantenha o Azure PowerShell aberto até o fim deste guia de início rápido. Se você fechar e reabrir, precisará executar os comandos novamente.

    Execute o comando a seguir e insira o nome de usuário e senha usados para entrar no portal do Azure:
        
    ```powershell
    Login-AzureRmAccount
    ```        
    Execute o comando abaixo para exibir todas as assinaturas dessa conta:

    ```powershell
    Get-AzureRmSubscription
    ```
    Execute o comando a seguir para selecionar a assinatura com a qual deseja trabalhar. Substitua **SubscriptionId** pela ID da assinatura do Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"    
    ```  
3. Crie o grupo de recursos ADFTutorialResourceGroup. 

    ```powershell
    New-AzureRmResourceGroup -Name $resourceGroupName -Location "East Us" 
    ```
4. Crie o data factory. 

    ```powershell
     $df = Set-AzureRmDataFactoryV2 -Location EastUS -Name $dataFactoryName -ResourceGroupName $resourceGroupName
    ```

    Execute o comando a seguir para ver a saída: 

    ```powershell
    $df
    ```
5. Mude para a pasta em que você criou arquivos JSON e execute o seguinte comando para implantar um serviços vinculado do Armazenamento do Azure: 
       
    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyStorageLinkedService" -File "MyStorageLinkedService.json"
    ```
6. Execute o comando a seguir para implantar um serviço vinculado do Spark sob demanda: 
       
    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyOnDemandSparkLinkedService" -File "MyOnDemandSparkLinkedService.json"
    ```
7. Execute o seguinte comando para implantar um pipeline: 
       
    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $pipelineName -File "MySparkOnDemandPipeline.json"
    ```
    
## <a name="start-and-monitor-a-pipeline-run"></a>Iniciar e monitorar uma execução de pipeline  

1. Inicie uma execução de pipeline. Ele também captura a ID da execução de pipeline para monitoramento futuro.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName  
    ```
2. Execute o script a seguir para verificar continuamente o status do pipeline de execução até que ele termine.

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    
        if(!$result) {
            Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
        }
        elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
        }
        else {
            Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
        ($result | Format-List | Out-String)
        Start-Sleep -Seconds 15
    }

    Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"

    Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n" 
    ```  
3. Aqui está a saída da execução de exemplo: 

    ```
    Pipeline run status: In Progress
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : 
    ActivityName      : MySparkActivity
    PipelineRunId     : 94e71d08-a6fa-4191-b7d1-cf8c71cb4794
    PipelineName      : MySparkOnDemandPipeline
    Input             : {rootPath, entryFilePath, getDebugInfo, sparkJobLinkedService}
    Output            : 
    LinkedServiceName : 
    ActivityRunStart  : 9/20/2017 6:33:47 AM
    ActivityRunEnd    : 
    DurationInMs      : 
    Status            : InProgress
    Error             :
    …
    
    Pipeline ' MySparkOnDemandPipeline' run finished. Result:
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : MyDataFactory09102017
    ActivityName      : MySparkActivity
    PipelineRunId     : 94e71d08-a6fa-4191-b7d1-cf8c71cb4794
    PipelineName      : MySparkOnDemandPipeline
    Input             : {rootPath, entryFilePath, getDebugInfo, sparkJobLinkedService}
    Output            : {clusterInUse, jobId, ExecutionProgress, effectiveIntegrationRuntime}
    LinkedServiceName : 
    ActivityRunStart  : 9/20/2017 6:33:47 AM
    ActivityRunEnd    : 9/20/2017 6:46:30 AM
    DurationInMs      : 763466
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity Output section:
    "clusterInUse": "https://ADFSparkSamplexxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.azurehdinsight.net/"
    "jobId": "0"
    "ExecutionProgress": "Succeeded"
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
    Activity Error section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "MySparkActivity"
    ```
4. Confirme que uma pasta denominada `outputfiles` é criada na pasta `spark` do contêiner adftutorial com a saída do programa Spark. 


## <a name="next-steps"></a>Próximas etapas
O pipeline nessa amostra copia dados de uma localização para outra em um Armazenamento de Blobs do Azure. Você aprendeu como: 

> [!div class="checklist"]
> * Criar uma fábrica de dados. 
> * Criar e implantar serviços vinculados.
> * Criar e implantar um pipeline. 
> * Inicie uma execução de pipeline.
> * Monitore a execução de pipeline.

Avance para o próximo tutorial para aprender como transformar dados executando o script Hive em um cluster do Azure HDInsight que está em uma rede virtual. 

> [!div class="nextstepaction"]
> [Tutorial: transformar dados usando o Hive na Rede Virtual do Azure](tutorial-transform-data-hive-virtual-network.md).





