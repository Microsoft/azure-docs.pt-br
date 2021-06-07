---
title: Copiar dados no Armazenamento de Blobs usando o Azure Data Factory
description: Crie um Azure Data Factory usando o PowerShell para copiar dados de uma localização no Armazenamento de Blobs do Azure para outra.
author: linda33wj
ms.service: data-factory
ms.devlang: powershell
ms.topic: quickstart
ms.date: 04/10/2020
ms.author: jingwang
ms.openlocfilehash: 9f419d89a9757a11055781335cbf98e9eb651548
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100372715"
---
# <a name="quickstart-create-an-azure-data-factory-using-powershell"></a>Início rápido: Criar um Azure Data Factory usando o PowerShell

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão atual](quickstart-create-data-factory-powershell.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este guia de início rápido descreve como usar o PowerShell para criar um Azure Data Factory. O pipeline que você criar nesse data factory **copia** dados de uma pasta para outra em um Armazenamento de Blobs do Azure. Para obter um tutorial sobre como **transformar** dados usando o Azure Data Factory, confira [Tutorial: Transformar dados usando o Spark](transform-data-using-spark.md).

> [!NOTE]
> Este artigo não fornece uma introdução detalhada do serviço Data Factory. Para obter uma introdução do serviço do Azure Data Factory, consulte [Introdução ao Azure Data Factory](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)]

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Instale os módulos mais recentes do Azure PowerShell seguindo as instruções em [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-Az-ps).

>[!WARNING]
>Se você não usar as versões mais recentes do PowerShell e o módulo do Data Factory, poderá encontrar erros de desserialização ao executar os comandos. 

#### <a name="log-in-to-powershell"></a>Fazer logon no PowerShell

1. Iniciar o **PowerShell** no seu computador. Mantenha o PowerShell aberto até o fim deste guia de início rápido. Se você fechá-la e reabri-la, precisará executar esses comandos novamente.

2. Execute o comando a seguir e insira o mesmo nome de usuário e senha do Azure que você usa para entrar no Portal do Azure:

    ```powershell
    Connect-AzAccount
    ```

3. Execute o comando abaixo para exibir todas as assinaturas dessa conta:

    ```powershell
    Get-AzSubscription
    ```

4. Se você vir várias assinaturas associadas à sua conta, execute o seguinte comando para selecionar a assinatura com que deseja trabalhar. Substitua **SubscriptionId** pela ID da assinatura do Azure:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"
    ```

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Defina uma variável para o nome do grupo de recursos que você usa nos comandos do PowerShell posteriormente. Copie o seguinte texto de comando para o PowerShell, especifique um nome para o [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) entre aspas duplas e, em seguida, execute o comando. Por exemplo: `"ADFQuickStartRG"`.

     ```powershell
    $resourceGroupName = "ADFQuickStartRG";
    ```

    Se o grupo de recursos já existir, não convém substituí-lo. Atribua um valor diferente para a variável `$ResourceGroupName` e execute o comando novamente

2. Para criar o grupo de recursos do Azure, execute o seguinte comando:

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'East US'
    ```

    Se o grupo de recursos já existir, não convém substituí-lo. Atribua um valor diferente para a variável `$ResourceGroupName` e execute o comando novamente.

3. Defina uma variável para o nome do data factory. 

    > [!IMPORTANT]
    >  Atualize o Nome do data factory para ser globalmente exclusivo. Por exemplo, ADFTutorialFactorySP1127.

    ```powershell
    $dataFactoryName = "ADFQuickStartFactory";
    ```

4. Para criar o data factory, execute o cmdlet **Set-AzDataFactoryV2** a seguir usando a propriedade Location e ResourceGroupName da variável $ResGrp:

    ```powershell
    $DataFactory = Set-AzDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName `
        -Location $ResGrp.Location -Name $dataFactoryName
    ```

Observe os seguintes pontos:

* O nome do Azure Data Factory deve ser globalmente exclusivo. Se você receber o erro a seguir, altere o nome e tente novamente.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Para criar instâncias de Data Factory, a conta de usuário usada para fazer logon no Azure deve ser um membro das funções **colaborador** ou **proprietário**, ou um **administrador** da assinatura do Azure.

* Para obter uma lista de regiões do Azure no qual o Data Factory está disponível no momento, selecione as regiões que relevantes para você na página a seguir e, em seguida, expanda **Análise** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outras regiões.


## <a name="create-a-linked-service"></a>Criar um serviço vinculado

Crie serviços vinculados em um data factory para vincular seus armazenamentos de dados e serviços de computação ao data factory. Neste guia de início rápido, você cria um serviço vinculado do Armazenamento do Azure que é usado como armazenamento de origem e do coletor. O serviço vinculado tem as informações de conexão que o serviço do Data Factory usa no runtime para se conectar a ele.

>[!TIP]
>Neste início rápido, você usa a *Chave de conta* como o tipo de autenticação para o armazenamento de dados, mas você pode escolher outros métodos de autenticação compatíveis: *URI de SAS*, *Entidade de Serviço* e *Identidade Gerenciada* se necessário. Veja as seções correspondentes [neste artigo](./connector-azure-blob-storage.md#linked-service-properties) para obter detalhes.
>Para armazenar segredos de armazenamentos de dados com segurança, também é recomendável usar um Azure Key Vault. Veja [este artigo](./store-credentials-in-key-vault.md) para obter ilustrações detalhadas.

1. Crie um arquivo JSON chamado **AzureStorageLinkedService.json** na pasta **C:\ADFv2QuickStartPSH** com o seguinte conteúdo: (Crie a pasta ADFv2QuickStartPSH se ela ainda não existir.).

    > [!IMPORTANT]
    > Antes de salvar o arquivo, substitua &lt;accountName&gt; e &lt;accountKey&gt; pelo nome e pela chave da sua conta de armazenamento do Azure, respectivamente.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "annotations": [],
            "type": "AzureBlobStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net"
            }
        }
    }
    ```

    Se você estiver usando o Bloco de Notas, selecione **Todos os arquivos** para o **Salvar como tipo** preenchido na caixa de diálogo **Salvar como**. Caso contrário, ele pode adicionar a extensão `.txt` para o arquivo. Por exemplo, `AzureStorageLinkedService.json.txt`. Se você criar o arquivo no Explorador de arquivos antes de abri-lo no Bloco de Notas, você não poderá ver a extensão `.txt`, já que a opção **Ocultar extensões de tipos de arquivos conhecidos** será definida por padrão. Remova a extensão `.txt` antes de prosseguir para a próxima etapa.

2. No **PowerShell**, mude para a pasta **ADFv2QuickStartPSH**.

    ```powershell
    Set-Location 'C:\ADFv2QuickStartPSH'
    ```

3. Execute o cmdlet **Set-AzDataFactoryV2LinkedService** para criar o serviço vinculado: **AzureStorageLinkedService**.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureStorageLinkedService" `
        -DefinitionFile ".\AzureStorageLinkedService.json"
    ```

    Veja o exemplo de saída:

    ```console
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobStorageLinkedService
    ```

## <a name="create-datasets"></a>Criar conjuntos de dados

Neste procedimento, você criará dois conjuntos de dados: **InputDataset** e **OutputDataset**. Esses conjuntos de dados são do tipo **Binário**. Eles se referem ao Serviço vinculado do Armazenamento do Azure que você criou na seção anterior.
O conjunto de dados de entrada representa os dados de origem na pasta de entrada. Na definição de conjunto de dados de entrada, especifique o contêiner de blob (**adftutorial**), a pasta (**entrada**) e o arquivo (**emp.txt**) que contém os dados de origem.
Esse conjunto de dados de saída representa os dados que são copiados para o destino. Na definição de conjunto de dados de saída, especifique o contêiner de blob (**adftutorial**), a pasta (**saída**) e o arquivo para o qual os dados são copiados. 
1. Crie um arquivo JSON chamado **InputDataset.json** na pasta **C:\ADFv2QuickStartPSH** com o seguinte conteúdo:

    ```json
    {
        "name": "InputDataset",
        "properties": {
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "annotations": [],
            "type": "Binary",
            "typeProperties": {
                "location": {
                    "type": "AzureBlobStorageLocation",
                    "fileName": "emp.txt",
                    "folderPath": "input",
                    "container": "adftutorial"
                }
            }
        }
    }
    ```

2. Para criar o conjunto de dados: **InputDataset**; execute o cmdlet **Set-AzDataFactoryV2Dataset**.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName -Name "InputDataset" `
        -DefinitionFile ".\InputDataset.json"
    ```

    Veja o exemplo de saída:

    ```console
    DatasetName       : InputDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.BinaryDataset
    ```

3. Repita as etapas para criar o conjunto de dados de saída. Crie um arquivo JSON chamado **OutputDataset.json** na pasta **C:\ADFv2QuickStartPSH** com o seguinte conteúdo:

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "annotations": [],
            "type": "Binary",
            "typeProperties": {
                "location": {
                    "type": "AzureBlobStorageLocation",
                    "folderPath": "output",
                    "container": "adftutorial"
                }
            }
        }
    }
    ```

4. Execute o cmdlet **Set-AzDataFactoryV2Dataset** para criar o **OutDataset**.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName -Name "OutputDataset" `
        -DefinitionFile ".\OutputDataset.json"
    ```

    Veja o exemplo de saída:

    ```console
    DatasetName       : OutputDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.BinaryDataset
    ```
## <a name="create-a-pipeline"></a>Criar um pipeline

Neste procedimento, você criará um pipeline com uma atividade de cópia que usa os conjuntos de dados de entrada e saída. A Atividade de cópia copia os dados do arquivo especificado por você nas configurações do conjunto de dados de entrada para o arquivo especificado por você nas configurações do conjunto de dados de saída.  

1. Crie um arquivo JSON denominado **Adfv2QuickStartPipeline.json** na pasta **C:\ADFv2QuickStartPSH** com o seguinte conteúdo:

    ```json
    {
        "name": "Adfv2QuickStartPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToBlob",
                    "type": "Copy",
                    "dependsOn": [],
                    "policy": {
                        "timeout": "7.00:00:00",
                        "retry": 0,
                        "retryIntervalInSeconds": 30,
                        "secureOutput": false,
                        "secureInput": false
                    },
                    "userProperties": [],
                    "typeProperties": {
                        "source": {
                            "type": "BinarySource",
                            "storeSettings": {
                                "type": "AzureBlobStorageReadSettings",
                                "recursive": true
                            }
                        },
                        "sink": {
                            "type": "BinarySink",
                            "storeSettings": {
                                "type": "AzureBlobStorageWriteSettings"
                            }
                        },
                        "enableStaging": false
                    },
                    "inputs": [
                        {
                            "referenceName": "InputDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "OutputDataset",
                            "type": "DatasetReference"
                        }
                    ]
                }
            ],
            "annotations": []
        }
    }
    ```

2. Para criar o pipeline: **Adfv2QuickStartPipeline**, execute o cmdlet **Set-AzDataFactoryV2Pipeline**.

    ```powershell
    $DFPipeLine = Set-AzDataFactoryV2Pipeline `
        -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName `
        -Name "Adfv2QuickStartPipeline" `
        -DefinitionFile ".\Adfv2QuickStartPipeline.json"
    ```

## <a name="create-a-pipeline-run"></a>Criar uma execução de pipeline

Nesta etapa, você criará uma execução de pipeline.

Execute o cmdlet **Invoke-AzDataFactoryV2Pipeline** para criar uma execução de pipeline. O cmdlet retorna a ID da execução de pipeline para monitoramento futuro.

  ```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline `
    -DataFactoryName $DataFactory.DataFactoryName `
    -ResourceGroupName $ResGrp.ResourceGroupName `
    -PipelineName $DFPipeLine.Name 
```

## <a name="monitor-the-pipeline-run"></a>Monitorar a execução de pipeline

1. Execute o script do PowerShell a seguir para verificar continuamente o status da execução de pipeline até que ela termine de copiar os dados. Copie/cole o script a seguir na janela do PowerShell e pressione ENTER.

    ```powershell
    while ($True) {
        $Run = Get-AzDataFactoryV2PipelineRun `
            -ResourceGroupName $ResGrp.ResourceGroupName `
            -DataFactoryName $DataFactory.DataFactoryName `
            -PipelineRunId $RunId

        if ($Run) {
            if ( ($Run.Status -ne "InProgress") -and ($Run.Status -ne "Queued") ) {
                Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
                $Run
                break
            }
            Write-Output ("Pipeline is running...status: " + $Run.Status)
        }

        Start-Sleep -Seconds 10
    }
    ```

    Eis aqui a amostra de saída da execução de pipeline:

    ```console
    Pipeline is running...status: InProgress
    Pipeline run finished. The status is:  Succeeded
    
    ResourceGroupName : ADFQuickStartRG
    DataFactoryName   : ADFQuickStartFactory
    RunId             : 00000000-0000-0000-0000-0000000000000
    PipelineName      : Adfv2QuickStartPipeline
    LastUpdated       : 8/27/2019 7:23:07 AM
    Parameters        : {}
    RunStart          : 8/27/2019 7:22:56 AM
    RunEnd            : 8/27/2019 7:23:07 AM
    DurationInMs      : 11324
    Status            : Succeeded
    Message           : 
    ```
2. Execute o script a seguir para recuperar os detalhes de execução da atividade de cópia, por exemplo, o tamanho dos dados lidos/gravados.

    ```powershell
    Write-Output "Activity run details:"
    $Result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineRunId $RunId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $Result

    Write-Output "Activity 'Output' section:"
    $Result.Output -join "`r`n"

    Write-Output "Activity 'Error' section:"
    $Result.Error -join "`r`n"
    ```
3. Verifique se você vê uma saída semelhante à amostra de saída do resultado da execução de atividade a seguir:

    ```console
    ResourceGroupName : ADFQuickStartRG
    DataFactoryName   : ADFQuickStartFactory
    ActivityRunId     : 00000000-0000-0000-0000-000000000000
    ActivityName      : CopyFromBlobToBlob
    PipelineRunId     : 00000000-0000-0000-0000-000000000000
    PipelineName      : Adfv2QuickStartPipeline
    Input             : {source, sink, enableStaging}
    Output            : {dataRead, dataWritten, filesRead, filesWritten...}
    LinkedServiceName :
    ActivityRunStart  : 8/27/2019 7:22:58 AM
    ActivityRunEnd    : 8/27/2019 7:23:05 AM
    DurationInMs      : 6828
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity 'Output' section:
    "dataRead": 20
    "dataWritten": 20
    "filesRead": 1
    "filesWritten": 1
    "sourcePeakConnections": 1
    "sinkPeakConnections": 1
    "copyDuration": 4
    "throughput": 0.01
    "errors": []
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (Central US)"
    "usedDataIntegrationUnits": 4
    "usedParallelCopies": 1
    "executionDetails": [
      {
        "source": {
          "type": "AzureBlobStorage"
        },
        "sink": {
          "type": "AzureBlobStorage"
        },
        "status": "Succeeded",
        "start": "2019-08-27T07:22:59.1045645Z",
        "duration": 4,
        "usedDataIntegrationUnits": 4,
        "usedParallelCopies": 1,
        "detailedDurations": {
          "queuingDuration": 3,
          "transferDuration": 1
        }
      }
    ]
    
    Activity 'Error' section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "CopyFromBlobToBlob"
    ```

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

O pipeline nessa amostra copia dados de uma localização para outra em um Armazenamento de Blobs do Azure. Percorra os [tutoriais](tutorial-copy-data-dot-net.md) para saber mais sobre o uso do Data Factory em mais cenários.
