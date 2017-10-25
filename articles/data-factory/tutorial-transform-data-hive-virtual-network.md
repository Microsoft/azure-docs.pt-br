---
title: Transformar dados usando o Hive na Rede Virtual do Azure | Microsoft Docs
description: "Este tutorial fornece instruções passo a passo para transformar dados usando a Atividade Hive no Azure Data Factory."
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
ms.openlocfilehash: ec472ea9f45a4c8bc18d065001eae381a275d185
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="transform-data-in-azure-virtual-network-using-hive-activity-in-azure-data-factory"></a>Transformar dados na Rede Virtual do Azure usando a Atividade Hive no Azure Data Factory
O Azure Data Factory é um serviço de integração de dados baseado em nuvem que permite que você crie fluxos de trabalho controlados por dados na nuvem para orquestrar e automatizar a movimentação e a transformação de dados. Usando o Azure Data Factory, você pode criar e agendar fluxos de trabalho orientados a dados (chamados de pipelines) que podem ingerir dados de repositórios de dados diferentes, processar/transformr os dados usando serviços de computação como o Hadoop do Azure HDInsight, Spark, Azure Data Lake Analytics e Azure Machine Learning e publicar os dados de saída em repositórios de dados como o SQL Data Warehouse do Azure para consumo pelos aplicativos de business intelligence (BI). 

Neste tutorial, você pode usar o Azure PowerShell para criar um pipeline do Data Factory que transforma dados usando a atividade Hive em um cluster HDInsight que está em uma Rede Virtual do Azure. Neste tutorial, você executa as seguintes etapas:

> [!div class="checklist"]
> * Criar uma fábrica de dados. 
> * Criar e configurar um Integration Runtime auto-hospedado
> * Criar e implantar serviços vinculados.
> * Criar e implantar um pipeline que contém uma atividade Hive.
> * Inicie uma execução de pipeline.
> * Monitorar a execução de pipeline 
> * Verificar a saída. 


Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
- **Conta de Armazenamento do Azure**. Você cria um script Hive e carrega-o no Armazenamento do Azure. A saída do script Hive é armazenada nessa conta de armazenamento. Nessa amostra, o cluster HDInsight usa essa conta de Armazenamento do Azure como o armazenamento primário. 
- **Rede Virtual do Azure.** Se você não tem uma Rede Virtual do Azure, crie-a seguindo [estas instruções](../virtual-network/virtual-network-get-started-vnet-subnet.md). Nessa amostra, o HDInsight está em uma Rede Virtual do Azure. Aqui está uma amostra de configuração de Rede Virtual do Azure. 

    ![Criar rede virtual](media/tutorial-transform-data-using-hive-in-vnet/create-virtual-network.png)
- **Cluster HDInsight.** Crie um cluster HDInsight e associe-o à rede virtual que você criou na etapa anterior seguindo este artigo: [Estender o Azure HDInsight usando uma Rede Virtual do Azure](../hdinsight/hdinsight-extend-hadoop-virtual-network.md). Aqui está uma amostra de configuração do HDInsight em uma Rede Virtual do Azure. 

    ![HDInsight em uma rede virtual](media/tutorial-transform-data-using-hive-in-vnet/hdinsight-in-vnet-configuration.png)
- **PowerShell do Azure**. Siga as instruções em [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-azurerm-ps).

### <a name="upload-hive-script-to-your-blob-storage-account"></a>Carregar o script Hive em sua conta de Armazenamento de Blobs

1. Crie um arquivo Hive SQL chamado **hivescript.hql** com o seguinte conteúdo:

   ```sql
   DROP TABLE IF EXISTS HiveSampleOut; 
   CREATE EXTERNAL TABLE HiveSampleOut (clientid string, market string, devicemodel string, state string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
   STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

   INSERT OVERWRITE TABLE HiveSampleOut
   Select 
       clientid,
       market,
       devicemodel,
       state
   FROM hivesampletable
   ```
2. No seu Armazenamento de Blobs do Azure, crie um contêiner denominado **adftutorial** se ele não existir.
3. Crie uma pasta chamada `hivescripts`.
4. Carregue o arquivo `hivescript.hql` na subpasta `hivescripts`.

 

## <a name="create-a-data-factory"></a>Criar uma data factory


1. Defina as variáveis uma a uma.

    ```powershell
    $subscriptionID = "<subscription ID>" # Your Azure subscription ID
    $resourceGroupName = "ADFTutorialResourceGroup" # Name of the resource group
    $dataFactoryName = "MyDataFactory09142017" # Globally unique name of the data factory
    $pipelineName = "MyHivePipeline" # Name of the pipeline
    $selfHostedIntegrationRuntimeName = "MySelfHostedIR09142017" # make it a unique name. 
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
3. Crie o grupo de recursos ADFTutorialResourceGroup se ele ainda não existir na sua assinatura. 

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

## <a name="create-self-hosted-ir"></a>Criar um IR auto-hospedado
Nesta seção, você cria um Integration Runtime auto-hospedado e associa-o a uma VM do Azure na mesma Rede Virtual do Azure em que está o seu cluster HDInsight.

1. Crie um Integration Runtime auto-hospedado. Use um nome exclusivo no caso exista outro Integration Runtime com o mesmo nome.

   ```powershell
   Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted
   ```
    Este comando cria um registro lógico do Integration Runtime auto-hospedado. 
2. Use o PowerShell para recuperar as chaves de autenticação para registrar o Integration Runtime auto-hospedado. Copie uma das chaves para registrar o Integration Runtime auto-hospedado.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName | ConvertTo-Json
   ```

   Veja o exemplo de saída: 

   ```powershell
   {
       "AuthKey1":  "IR@0000000000000000000000000000000000000=",
       "AuthKey2":  "IR@0000000000000000000000000000000000000="
   }
   ```
    Anote o valor de **AuthKey1** sem aspas. 
3. Crie uma VM do Azure e ingresse-a na mesma rede virtual que contém seu cluster HDInsight. Para obter detalhes, consulte [Como criar máquinas virtuais](../virtual-network/virtual-network-get-started-vnet-subnet.md#create-vms). Ingresse-as em uma rede virtual do Azure. 
4. Na VM do Azure, baixe o [Integration Runtime auto-hospedado](https://www.microsoft.com/download/details.aspx?id=39717). Use a chave de autenticação obtida na etapa anterior para registrar manualmente o Integration Runtime auto-hospedado. 

   ![Registrar Integration Runtime](media/tutorial-transform-data-using-hive-in-vnet/register-integration-runtime.png)

   Você verá a seguinte mensagem quando o Integration Runtime auto-hospedado for registrado com êxito: ![Registrado com êxito](media/tutorial-transform-data-using-hive-in-vnet/registered-successfully.png)

   Você verá a página a seguir quando o nó estiver conectado ao serviço de nuvem: ![O nó está conectado](media/tutorial-transform-data-using-hive-in-vnet/node-is-connected.png)

## <a name="author-linked-services"></a>Criar serviços vinculados

Você cria e implanta dois serviços vinculados nesta seção:
- Um serviço vinculado do Armazenamento do Azure que vincula uma conta de Armazenamento do Azure ao data factory. Esse armazenamento é o armazenamento primário usado por seu cluster HDInsight. Nesse caso, podemos também usar essa conta de Armazenamento do Azure para manter o script Hive e a saída do script.
- Um serviço vinculado do HDInsight. O Azure Data Factory envia o script Hive para este cluster do HDInsight para execução.

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
      },
      "connectVia": {
        "referenceName": "MySelfhostedIR",
        "type": "IntegrationRuntimeReference"
      }  
    }
}
```

Substitua **&lt;accountname&gt; e &lt;accountkey&gt;** pelo nome e pela chave da sua conta de Armazenamento do Azure, respectivamente.

### <a name="hdinsight-linked-service"></a>Serviço vinculado ao HDInsight

Crie um arquivo JSON usando seu editor preferido, copie a seguinte definição de JSON de um serviço vinculado do Azure HDInsight e, em seguida, salve o arquivo como **MyHDInsightLinkedService.json**.

```
{
  "name": "MyHDInsightLinkedService",
  "properties": {     
      "type": "HDInsight",
      "typeProperties": {
          "clusterUri": "https://<clustername>.azurehdinsight.net",
          "userName": "<username>",
          "password": {
            "value": "<password>",
            "type": "SecureString"
          },
          "linkedServiceName": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
      },
      "connectVia": {
        "referenceName": "MySelfhostedIR",
        "type": "IntegrationRuntimeReference"
      }
  }
}
```

Atualize os valores para as propriedades a seguir na definição de serviço vinculado:

- **userName**. Nome usuário de logon do cluster especificado ao criar o cluster. 
- **password**. A senha do usuário.
- **clusterUri**. Especifique a URL do cluster HDInsight no formato https://<clustername>.azurehdinsight.net.  Este artigo pressupõe que você tenha acesso ao cluster via Internet. Por exemplo, que você possa se conectar ao cluster em `https://clustername.azurehdinsight.net`. Esse endereço usa o gateway público, que não estará disponível se você tiver usado NSGs (Grupos de Segurança de Rede) ou UDRs (rotas definidas pelo usuário) para restringir o acesso da Internet. Para que o Data Factory possa enviar trabalhos para o cluster HDInsight na Rede Virtual do Azure, você precisa configurar sua Rede Virtual do Azure de modo que a URL possa ser resolvida para o endereço IP do gateway usado pelo HDInsight.

  1. No Portal do Azure, abra a Rede Virtual que contém o HDInsight. Abra o adaptador de rede cujo nome começa com `nic-gateway-0`. Anote o endereço IP privado dela. Por exemplo, 10.6.0.15. 
  2. Se sua Rede Virtual do Azure tem um servidor DNS, atualize o registro DNS de modo que a URL do cluster do HDInsight `https://<clustername>.azurehdinsight.net` possa ser resolvida para `10.6.0.15`. Essa é a abordagem recomendada. Se você não tiver um servidor DNS em sua Rede Virtual do Azure, você poderá usar uma solução alternativa temporária para isso editando o arquivo de hosts (C:\Windows\System32\drivers\etc.) de todas as VMs registradas como nós de Integration Runtime auto-hospedado, adicionando uma entrada como esta: 
  
        `10.6.0.15 myHDIClusterName.azurehdinsight.net`

Mude para a pasta em que você criou arquivos JSON e execute o seguinte comando para implantar os serviços vinculados: 


```powershell
Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyStorageLinkedService" -File "MyStorageLinkedService.json"

Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyHDILinkedService" -File "MyHDILinkedService.json"
```

## <a name="author-a-pipeline"></a>Criar um pipeline
Nesta etapa, você cria um pipeline com uma atividade Hive. A atividade executa o script do Hive para retornar dados de uma tabela de exemplo e salvá-los em um caminho que você definiu. Crie um arquivo JSON em seu editor preferido, copie a definição de JSON a seguir de uma definição de pipeline e salve-a como **MyHiveOnDemandPipeline.json**.


```json
{
  "name": "MyHivePipeline",
  "properties": {
    "activities": [
      {
        "name": "MyHiveActivity",
        "type": "HDInsightHive",
        "linkedServiceName": {
            "referenceName": "MyHDILinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "scriptPath": "adftutorial\\hivescripts\\hivescript.hql",
          "getDebugInfo": "Failure",
          "defines": {           
            "Output": "wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/"
          },
          "scriptLinkedService": {
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

- **scriptPath** aponta para o caminho para o script Hive na conta de Armazenamento do Azure que você usou para MyStorageLinkedService. O caminho diferencia maiúsculas de minúsculas.
- **Output** é um argumento usado no script Hive. Use o formato de `wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` para apontá-lo para uma pasta existente no seu Armazenamento do Azure. O caminho diferencia maiúsculas de minúsculas. 

Mude para a pasta em que você criou arquivos JSON e execute o seguinte comando para implantar o pipeline: 


```powershell
Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $pipelineName -File "MyHivePipeline.json"
```

## <a name="start-the-pipeline"></a>Iniciar o pipeline 

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

   Aqui está a saída da execução de exemplo:

    ```json
    Pipeline run status: In Progress
    
    ResourceGroupName : ADFV2SampleRG2
    DataFactoryName   : SampleV2DataFactory2
    ActivityName      : MyHiveActivity
    PipelineRunId     : 000000000-0000-0000-000000000000000000
    PipelineName      : MyHivePipeline
    Input             : {getDebugInfo, scriptPath, scriptLinkedService, defines}
    Output            :
    LinkedServiceName :
    ActivityRunStart  : 9/18/2017 6:58:13 AM
    ActivityRunEnd    :
    DurationInMs      :
    Status            : InProgress
    Error             :
    
    Pipeline ' MyHivePipeline' run finished. Result:
    
    ResourceGroupName : ADFV2SampleRG2
    DataFactoryName   : SampleV2DataFactory2
    ActivityName      : MyHiveActivity
    PipelineRunId     : 0000000-0000-0000-0000-000000000000
    PipelineName      : MyHivePipeline
    Input             : {getDebugInfo, scriptPath, scriptLinkedService, defines}
    Output            : {logLocation, clusterInUse, jobId, ExecutionProgress...}
    LinkedServiceName :
    ActivityRunStart  : 9/18/2017 6:58:13 AM
    ActivityRunEnd    : 9/18/2017 6:59:16 AM
    DurationInMs      : 63636
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity Output section:
    "logLocation": "wasbs://adfjobs@adfv2samplestor.blob.core.windows.net/HiveQueryJobs/000000000-0000-47c3-9b28-1cdc7f3f2ba2/18_09_2017_06_58_18_023/Status"
    "clusterInUse": "https://adfv2HivePrivate.azurehdinsight.net"
    "jobId": "job_1505387997356_0024"
    "ExecutionProgress": "Succeeded"
    "effectiveIntegrationRuntime": "MySelfhostedIR"
    Activity Error section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "MyHiveActivity"
    ```
4. Verifique a pasta `outputfolder` em busca do novo arquivo criado como resultado da consulta de Hive, ele deve se parecer com a amostra de saída a seguir: 

   ```
   8 en-US SCH-i500 California
   23 en-US Incredible Pennsylvania
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   246 en-US SCH-i500 District Of Columbia
   246 en-US SCH-i500 District Of Columbia
   ```

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você realizou as seguintes etapas: 

> [!div class="checklist"]
> * Criar uma fábrica de dados. 
> * Criar e configurar um Integration Runtime auto-hospedado
> * Criar e implantar serviços vinculados.
> * Criar e implantar um pipeline que contém uma atividade Hive.
> * Inicie uma execução de pipeline.
> * Monitorar a execução de pipeline 
> * Verificar a saída. 

Avance para o tutorial a seguir para saber mais sobre como transformar dados usando um cluster Spark no Azure:

> [!div class="nextstepaction"]
>[Ramificação e encadeamento de fluxo de controle de Data Factory](tutorial-control-flow.md)



