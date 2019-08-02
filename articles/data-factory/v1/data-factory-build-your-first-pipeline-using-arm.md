---
title: Criar seu primeiro data factory (modelo do Resource Manager) | Microsoft Docs
description: Neste tutorial, você criará um pipeline de exemplo do Azure Data Factory usando um modelo do Azure Resource Manager.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: ''
editor: ''
ms.assetid: eb9e70b9-a13a-4a27-8256-2759496be470
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 66475c0500cb3106dc95945dd2e457e20f68bff3
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836661"
---
# <a name="tutorial-build-your-first-azure-data-factory-using-azure-resource-manager-template"></a>Tutorial: Criar seu primeiro Azure data factory usando o modelo do Azure Resource Manager
> [!div class="op_single_selector"]
> * [Visão geral e pré-requisitos](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Modelo do Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
> * [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)
> 
 
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Caso esteja usando a versão atual do serviço Data Factory, consulte [Início Rápido: Criar um data factory usando o Azure Data Factory](../quickstart-create-data-factory-dot-net.md).

Neste artigo, você usa os modelos do Azure Resource Manager para criar seu primeiro data factory do Azure. Para fazer o tutorial usando outras ferramentas/SDKs, selecione uma das opções da lista suspensa.

O pipeline deste tutorial tem uma atividade: **Atividade do Hive no HDInsight**. Esta atividade executa um script de hive em um cluster do HDInsight do Azure que transforma os dados de entrada para gerar dados de saída. O pipeline é agendado para ser executado uma vez por mês entre os horários de início e término especificados. 

> [!NOTE]
> O pipeline de dados neste tutorial transforma os dados de entrada para gerar dados de saída. Para obter um tutorial sobre como copiar dados usando o Azure Data Factory, confira [Tutorial: Copiar dados do Armazenamento de Blobs para o Banco de Dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> O pipeline deste tutorial tem apenas uma atividade do tipo: HDInsightHive. Um pipeline pode ter mais de uma atividade. E você pode encadear duas atividades (executar uma atividade após a outra) definindo o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. Para saber mais, confira [Agendamento e execução no Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Leia o artigo [Visão geral do tutorial](data-factory-build-your-first-pipeline.md) e concluir as etapas de **pré-requisito** .
* Siga as instruções do artigo [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para instalar a última versão do Azure PowerShell no computador.
* Veja [Criando modelos do Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) para saber mais sobre os modelos do Azure Resource Manager. 

## <a name="in-this-tutorial"></a>Neste tutorial

| Entidade | DESCRIÇÃO |
| --- | --- |
| Serviço vinculado de armazenamento do Azure |Vincula sua conta de Armazenamento do Azure Data Factory. A conta do Armazenamento do Azure manterá os dados de entrada e de saída para o pipeline neste exemplo. |
| Serviço vinculado do HDInsight sob demanda |Vincula um cluster HDInsight sob demanda ao data factory. O cluster é criado automaticamente para você para processar dados e é excluído depois que o processamento é concluído. |
| Conjunto de dados de entrada de Blob do Azure |Refere-se ao serviço vinculado do Armazenamento do Azure. O serviço vinculado refere-se a uma conta de Armazenamento do Azure e o conjunto de dados de Blob do Azure especifica o contêiner, a pasta e o nome do arquivo no armazenamento que contém os dados de entrada. |
| Conjunto de dados de saída de Blob do Azure |Refere-se ao serviço vinculado do Armazenamento do Azure. O serviço vinculado refere-se a uma conta de Armazenamento do Azure e o conjunto de dados de Blob do Azure especifica o contêiner, a pasta e o nome do arquivo no armazenamento que contém os dados de saída. |
| Pipeline de dados |O pipeline tem uma atividade do tipo HDInsightHive, que consome o conjunto de dados de entrada e produz o conjunto de dados de saída. |

Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline em um data factory pode ter uma ou mais atividades. Há dois tipos de atividades: [atividades de movimentação de dados](data-factory-data-movement-activities.md) e [atividades de transformação de dados](data-factory-data-transformation-activities.md). Neste tutorial, você criará um pipeline com uma atividade (atividade do Hive).

A seção a seguir fornece o modelo do Resource Manager completo para definir entidades de Data Factory de modo que você possa percorrer o tutorial rapidamente e testar o modelo. Para entender como cada entidade de Data Factory é definida, consulte a seção [Entidades de Data Factory no modelo](#data-factory-entities-in-the-template). Para saber mais sobre a sintaxe JSON e as propriedades de recursos do Data Factory em um modelo, confira [Tipos de recurso de Microsoft.DataFactory](/azure/templates/microsoft.datafactory/allversions).

## <a name="data-factory-json-template"></a>Modelo de JSON do Data Factory
O modelo do Resource Manager de nível superior para definir um data factory é: 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
        {
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "westus",
            "resources": [
                { ... },
                { ... },
                { ... },
                { ... }
            ]
        }
    ]
}
```
Crie um arquivo JSON denominado **ADFTutorialARM.json** na pasta **C:\ADFGetStarted** com este conteúdo:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the input/output data." } },
          "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
          "blobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
          "inputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that has the input file." } },
          "inputBlobName": { "type": "string", "metadata": { "description": "Name of the input file/blob." } },
          "outputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that will hold the transformed data." } },
          "hiveScriptFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that contains the Hive query file." } },
          "hiveScriptFile": { "type": "string", "metadata": { "description": "Name of the hive query (HQL) file." } }
    },
    "variables": {
          "dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",
          "azureStorageLinkedServiceName": "AzureStorageLinkedService",
          "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
          "blobInputDatasetName": "AzureBlobInput",
          "blobOutputDatasetName": "AzureBlobOutput",
          "pipelineName": "HiveTransformPipeline"
    },
    "resources": [
      {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "West US",
        "resources": [
          {
            "type": "linkedservices",
            "name": "[variables('azureStorageLinkedServiceName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureStorage",
                  "description": "Azure Storage linked service",
                  "typeProperties": {
                    "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
                  }
            }
          },
          {
            "type": "linkedservices",
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "HDInsightOnDemand",
                  "typeProperties": {
                    "version": "3.5",
                    "clusterSize": 1,
                    "timeToLive": "00:05:00",
                    "osType": "Linux",
                    "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
                  }
            }
          },
          {
            "type": "datasets",
            "name": "[variables('blobInputDatasetName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureBlob",
                  "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                  "typeProperties": {
                    "fileName": "[parameters('inputBlobName')]",
                    "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
                    "format": {
                          "type": "TextFormat",
                          "columnDelimiter": ","
                    }
                  },
                  "availability": {
                    "frequency": "Month",
                    "interval": 1
                  },
                  "external": true
            }
          },
          {
            "type": "datasets",
            "name": "[variables('blobOutputDatasetName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "type": "AzureBlob",
                  "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                  "typeProperties": {
                    "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
                    "format": {
                          "type": "TextFormat",
                          "columnDelimiter": ","
                    }
                  },
                  "availability": {
                    "frequency": "Month",
                    "interval": 1
                  }
            }
          },
          {
            "type": "datapipelines",
            "name": "[variables('pipelineName')]",
            "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]",
                  "[variables('hdInsightOnDemandLinkedServiceName')]",
                  "[variables('blobInputDatasetName')]",
                  "[variables('blobOutputDatasetName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
                  "description": "Pipeline that transforms data using Hive script.",
                  "activities": [
                {
                      "type": "HDInsightHive",
                      "typeProperties": {
                        "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                        "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                        "defines": {
                              "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                              "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                        }
                      },
                      "inputs": [
                        {
                              "name": "[variables('blobInputDatasetName')]"
                        }
                      ],
                      "outputs": [
                        {
                              "name": "[variables('blobOutputDatasetName')]"
                        }
                      ],
                      "policy": {
                        "concurrency": 1,
                        "retry": 3
                      },
                      "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                      },
                      "name": "RunSampleHiveActivity",
                      "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
                }
                  ],
                  "start": "2017-07-01T00:00:00Z",
                  "end": "2017-07-02T00:00:00Z",
                  "isPaused": false
              }
          }
        ]
      }
    ]
}
```

> [!NOTE]
> Encontre outro exemplo de modelo do Resource Manager para criação de um Azure data factory no [Tutorial: Criar um pipeline com a Atividade de Cópia usando um modelo do Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md).  
> 
> 

## <a name="parameters-json"></a>Parâmetros JSON
Crie um arquivo JSON chamado **ADFTutorialARM-Parameters.json** que contenha os parâmetros para o modelo do Azure Resource Manager.  

> [!IMPORTANT]
> Especifique o nome e a chave da conta de armazenamento do Azure para os parâmetros **storageAccountName** e **storageAccountKey** nesse arquivo de parâmetro. 
> 
> 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountName": {
            "value": "<Name of your Azure Storage account>"
        },
        "storageAccountKey": {
            "value": "<Key of your Azure Storage account>"
        },
        "blobContainer": {
            "value": "adfgetstarted"
        },
        "inputBlobFolder": {
            "value": "inputdata"
        },
        "inputBlobName": {
            "value": "input.log"
        },
        "outputBlobFolder": {
            "value": "partitioneddata"
        },
        "hiveScriptFolder": {
              "value": "script"
        },
        "hiveScriptFile": {
              "value": "partitionweblogs.hql"
        }
    }
}
```

> [!IMPORTANT]
> Você pode ter arquivos JSON de parâmetros separados para desenvolvimento, teste e ambientes de produção que pode usar com o mesmo modelo JSON do Data Factory. Usando um script do PowerShell, você pode automatizar a implantação de entidades de Data Factory nesses ambientes. 
> 
> 

## <a name="create-data-factory"></a>Criar um data factory
1. Inicie o **Azure PowerShell** e execute o comando a seguir: 
   * Execute o comando a seguir e insira o nome de usuário e a senha que você usa para entrar no portal do Azure.
     ```PowerShell
     Connect-AzAccount
     ```  
   * Execute o comando a seguir para exibir todas as assinaturas dessa conta.
     ```PowerShell
     Get-AzSubscription
     ``` 
   * Execute o comando a seguir para selecionar a assinatura com a qual deseja trabalhar. Esta assinatura deve ser igual à que você usou no portal do Azure.
     ```
     Get-AzSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzContext
     ```   
2. Execute o comando a seguir para implantar entidades do Data Factory usando o modelo do Resource Manager criado na Etapa 1. 

    ```PowerShell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFTutorialARM-Parameters.json
    ```

## <a name="monitor-pipeline"></a>Monitorar o pipeline
1. Depois de fazer logon no [portal do Azure](https://portal.azure.com/), clique em **Procurar** e selecione **Data factories**.
     ![Procurar->Data factories](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2. Na folha **Data Factories**, clique no data factory (**TutorialFactoryARM**) que você criou.    
3. Na folha **Data Factory** do seu data factory, clique em **Diagrama**.

     ![Bloco Diagrama](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4. Na **Exibição de Diagrama**, você tem uma visão geral dos pipelines e dos conjuntos de dados usados neste tutorial.
   
   ![Exibição de Diagrama](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
5. Na Exibição de diagrama, clique duas vezes no conjunto de dados **AzureBlobOutput**. Você verá a fatia que está sendo processada.
   
    ![Conjunto de dados](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
6. Quando o processamento for concluído, você verá a fatia no estado **Pronto** . A criação de um cluster do HDInsight sob demanda geralmente leva algum tempo (20 minutos, aproximadamente). Portanto, espere que o pipeline demore **cerca de 30 minutos** para processar a fatia.
   
    ![Conjunto de dados](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)    
7. Quando a fatia estiver no estado **Pronto**, verifique a pasta **partitioneddata** no contêiner **adfgetstarted** em seu armazenamento de blobs para os dados de saída.  

Confira [Monitorar os conjuntos de dados e o pipeline](data-factory-monitor-manage-pipelines.md) para obter instruções sobre como usar as folhas do portal do Azure para monitorar o pipeline e os conjuntos de dados que você criou neste tutorial.

Você também pode usar Monitorar e gerenciar aplicativos para monitorar os pipelines de dados. Veja [Monitorar e gerenciar os pipelines do Azure Data Factory usando o Aplicativo de Monitoramento](data-factory-monitor-manage-app.md) para obter detalhes sobre a utilização do aplicativo. 

> [!IMPORTANT]
> O arquivo de entrada é excluído quando a fatia é processada com êxito. Portanto, se você quiser executar novamente a fatia ou fazer o tutorial novamente, carregue o arquivo de entrada (input.log) na pasta inputdata do contêiner adfgetstarted.
> 
> 

## <a name="data-factory-entities-in-the-template"></a>Entidades do Data Factory no modelo
### <a name="define-data-factory"></a>Definir Data Factory
Você pode definir um Data Factory no modelo do Resource Manager, conforme mostrado no exemplo a seguir:  

```json
"resources": [
{
    "name": "[variables('dataFactoryName')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "West US"
}
```
O dataFactoryName é definido como: 

```json
"dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",
```
É uma cadeia de caracteres exclusiva com base na ID de grupo de recursos.  

### <a name="defining-data-factory-entities"></a>Definir entidades de Data Factory
As seguintes entidades de Data Factory são definidas no modelo JSON: 

* [Serviço vinculado de armazenamento do Azure](#azure-storage-linked-service)
* [Serviço vinculado do HDInsight sob demanda](#hdinsight-on-demand-linked-service)
* [Conjunto de dados de entrada de Blob do Azure](#azure-blob-input-dataset)
* [Conjunto de dados de saída do blob do Azure](#azure-blob-output-dataset)
* [Pipeline com a Atividade de cópia](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Serviço vinculado de armazenamento do Azure
Especifique o nome e a chave da sua conta de armazenamento do Azure nesta seção. Confira [Serviço vinculado de armazenamento do Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) para obter detalhes sobre os elementos JSON para definir um serviço vinculado de armazenamento do Azure. 

```json
{
    "type": "linkedservices",
    "name": "[variables('azureStorageLinkedServiceName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureStorage",
        "description": "Azure Storage linked service",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```
A **connectionString** usa os parâmetros storageAccountName e storageAccountKey. Os valores para esses parâmetros são passados pelo uso de um arquivo de configuração. A definição também usa variáveis: azureStorageLinkedService e dataFactoryName definidos no modelo. 

#### <a name="hdinsight-on-demand-linked-service"></a>Serviço vinculado do HDInsight sob demanda
Confira o artigo [Serviços vinculados de computação](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes sobre as propriedades JSON usadas para definir um serviço vinculado do HDInsight sob demanda.  

```json
{
    "type": "linkedservices",
    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
        }
    }
}
```
Observe os seguintes pontos: 

* O Data Factory cria um cluster HDInsight **baseado no Linux** para você com o JSON acima. Confira [Serviço vinculado do HDInsight sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes. 
* Você pode usar **seu próprio cluster do HDInsight** em vez de usar um cluster do HDInsight sob demanda. Confira [Serviço vinculado do HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) para obter detalhes.
* O cluster HDInsight cria um **contêiner padrão** no armazenamento de blobs especificado no JSON (**nomeServiçoVinculado**). O HDInsight não exclui esse contêiner quando o cluster é excluído. Este comportamento ocorre por design. Com o serviço vinculado HDInsight sob demanda, um cluster HDInsight é criado sempre que uma fatia precisa ser processada, a menos que haja um cluster ativo existente (**timeToLive**), e é excluído quando o processamento é concluído.
  
    Quanto mais fatias forem processadas, você verá muitos contêineres no armazenamento de blobs do Azure. Se você não precisa deles para solução de problemas dos trabalhos, convém excluí-los para reduzir o custo de armazenamento. Os nomes desses contêineres seguem um padrão: "adf**nomeseudatafactory**-**nomeserviçovinculado**- carimbodatahora". Use ferramentas como o [Gerenciador de Armazenamento da Microsoft](https://storageexplorer.com/) para excluir contêineres do armazenamento de blobs do Azure.

Confira [Serviço vinculado do HDInsight sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes.

#### <a name="azure-blob-input-dataset"></a>Conjunto de dados de entrada de Blob do Azure
Você especifica os nomes do contêiner de blob, da pasta e do arquivo que contém os dados de entrada. Confira [Propriedades de conjunto de dados de Blob do Azure](data-factory-azure-blob-connector.md#dataset-properties) para obter detalhes sobre os propriedades JSON usadas para definir um conjunto de dados de Blob do Azure. 

```json
{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
        "typeProperties": {
            "fileName": "[parameters('inputBlobName')]",
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true
    }
}
```
Essa definição usa os seguintes parâmetros definidos no modelo de parâmetro: blobContainer, inputBlobFolder e inputBlobName. 

#### <a name="azure-blob-output-dataset"></a>Conjunto de dados de saída de Blob do Azure
Especifique os nomes de contêiner de blob e a pasta que contém os dados de saída. Confira [Propriedades de conjunto de dados de Blob do Azure](data-factory-azure-blob-connector.md#dataset-properties) para obter detalhes sobre os propriedades JSON usadas para definir um conjunto de dados de Blob do Azure.  

```json
{
    "type": "datasets",
    "name": "[variables('blobOutputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
        "typeProperties": {
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        }
    }
}
```

Essa definição usa os seguintes parâmetros definidos no modelo de parâmetro: blobContainer e outputBlobFolder. 

#### <a name="data-pipeline"></a>Pipeline de dados
Você define um pipeline que transforma dados executando o script Hive em um cluster do Azure HDInsight sob demanda. Confira [JSON de Pipeline](data-factory-create-pipelines.md#pipeline-json) para obter descrições dos elementos JSON usados para definir um pipeline neste exemplo. 

```json
{
    "type": "datapipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]",
        "[variables('hdInsightOnDemandLinkedServiceName')]",
        "[variables('blobInputDatasetName')]",
        "[variables('blobOutputDatasetName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "description": "Pipeline that transforms data using Hive script.",
        "activities": [
        {
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                "defines": {
                    "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                    "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                }
            },
            "inputs": [
            {
                "name": "[variables('blobInputDatasetName')]"
            }
            ],
            "outputs": [
            {
                "name": "[variables('blobOutputDatasetName')]"
            }
            ],
            "policy": {
                "concurrency": 1,
                "retry": 3
            },
            "scheduler": {
                "frequency": "Month",
                "interval": 1
            },
            "name": "RunSampleHiveActivity",
            "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
        }
        ],
        "start": "2017-07-01T00:00:00Z",
        "end": "2017-07-02T00:00:00Z",
        "isPaused": false
    }
}
```

## <a name="reuse-the-template"></a>Reutilizar o modelo
No tutorial, você criou um modelo para definir entidades de Data Factory e um modelo para passar valores para parâmetros. Para usar o mesmo modelo para implantar as entidades de Data Factory em ambientes diferentes, você cria um arquivo de parâmetro para cada ambiente e usa-o ao implantar esse ambiente.     

Exemplo:  

```PowerShell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Dev.json

New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Test.json

New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Production.json
```
Observe que o primeiro comando usa o arquivo de parâmetro para o ambiente de desenvolvimento, outro para o ambiente de teste e um terceiro para o ambiente de produção.  

Também é possível reutilizar o modelo para executar tarefas repetidas. Por exemplo, você precisa criar vários data factories com um ou mais pipelines que implementem a mesma lógica, mas cada data factory usa contas de Banco de Dados SQL do Azure e Armazenamento do Azure diferentes. Nesse cenário, você usa o mesmo modelo no mesmo ambiente (desenvolvimento, teste ou produção) com arquivos de parâmetros diferentes para criar data factories. 

## <a name="resource-manager-template-for-creating-a-gateway"></a>Modelo do Resource Manager para criar um gateway
Aqui está um exemplo de modelo do Resource Manager para criar um gateway lógico na parte traseira. Instale um gateway em seu computador local ou na VM IaaS do Azure e registrar o gateway no serviço Data Factory usando uma chave. Confira [Mover dados entre o local e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter detalhes.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
    },
    "variables": {
        "dataFactoryName":  "GatewayUsingArmDF",
        "apiVersion": "2015-10-01",
        "singleQuote": "'"
    },
    "resources": [
        {
            "name": "[variables('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "eastus",
            "resources": [
                {
                    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
                    "type": "gateways",
                    "apiVersion": "[variables('apiVersion')]",
                    "name": "GatewayUsingARM",
                    "properties": {
                        "description": "my gateway"
                    }
                }            
            ]
        }
    ]
}
```
O modelo cria um data factory chamado GatewayUsingArmDF com um gateway chamado: GatewayUsingARM. 

## <a name="see-also"></a>Veja também

| Tópico | DESCRIÇÃO |
|:--- |:--- |
| [Pipelines](data-factory-create-pipelines.md) |Este artigo o ajuda a compreender pipelines e atividades no Azure Data Factory e como usá-los para construir fluxos de trabalho orientados a dados de ponta a ponta para seu cenário ou negócio. |
| [Conjunto de dados](data-factory-create-datasets.md) |Este artigo o ajuda a entender os conjuntos de dados no Azure Data Factory. |
| [Agendamento e execução](data-factory-scheduling-and-execution.md) |Este artigo explica os aspectos de agendamento e execução do modelo de aplicativo do Azure Data Factory. |
| [Monitorar e gerenciar pipelines usando o Aplicativo de Monitoramento](data-factory-monitor-manage-app.md) |Este artigo descreve como monitorar, gerenciar e depurar seus pipelines usando o Aplicativo de Monitoramento e Gerenciamento. |

