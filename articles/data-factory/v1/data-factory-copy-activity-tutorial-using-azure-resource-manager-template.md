---
title: 'Tutorial: Criar um pipeline usando o modelo do Resource Manager | Microsoft Docs'
description: Neste tutorial, você criará um pipeline do Azure Data Factory usando um modelo do Azure Resource Manager. Esse pipeline copia dados de um armazenamento de blobs do Azure para um banco de dados SQL do Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 1274e11a-e004-4df5-af07-850b2de7c15e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f4d972620d03bb856f06040f0a9b2d6f53f5873b
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836609"
---
# <a name="tutorial-use-azure-resource-manager-template-to-create-a-data-factory-pipeline-to-copy-data"></a>Tutorial: Usar um modelo do Azure Resource Manager para criar um pipeline do Data Factory para copiar dados 
> [!div class="op_single_selector"]
> * [Visão geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modelo do Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API do .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte o [tutorial de atividade de cópia](../quickstart-create-data-factory-dot-net.md). 

Este tutorial mostra como usar um modelo do Azure Resource Manager para criar um Azure Data Factory. O pipeline de dados neste tutorial copia os dados de um armazenamento de dados de origem para um armazenamento de dados de destino. Ele não transforma dados de entrada para gerar dados de saída. Para obter um tutorial sobre como transformar dados usando o Azure Data Factory, confira [Tutorial: Criar um pipeline para transformar dados usando um cluster Hadoop](data-factory-build-your-first-pipeline.md).

Neste tutorial, você criará um pipeline com uma atividade: Atividade de Cópia. A atividade de cópia copia dados de um armazenamento de dados com suporte para um armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens e coletores, confira [Armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats). A atividade é habilitada por um serviço globalmente disponível que pode copiar dados entre vários repositórios de dados de forma segura, confiável e escalonável. Para saber mais sobre a atividade de cópia, confira [Atividades de movimentação de dados](data-factory-data-movement-activities.md).

Um pipeline pode ter mais de uma atividade. E você pode encadear duas atividades (executar uma atividade após a outra) definindo o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. Para saber mais, confira [Várias atividades em um pipeline](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

> [!NOTE] 
> O pipeline de dados neste tutorial copia os dados de um armazenamento de dados de origem para um armazenamento de dados de destino. Para obter um tutorial sobre como transformar dados usando o Azure Data Factory, confira [Tutorial: Criar um pipeline para transformar dados usando um cluster Hadoop](data-factory-build-your-first-pipeline.md). 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Percorra o artigo [Pré-requisitos e Visão Geral do Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) e conclua as etapas de **pré-requisito**.
* Siga as instruções do artigo [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para instalar a última versão do Azure PowerShell no computador. Neste tutorial, você usa o PowerShell para implantar as entidades de Data Factory. 
* (opcional) Veja [Criando modelos do Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) para saber mais sobre os modelos do Azure Resource Manager.

## <a name="in-this-tutorial"></a>Neste tutorial
Neste tutorial, você pode criar um data factory com as seguintes entidades de Data Factory:

| Entidade | DESCRIÇÃO |
| --- | --- |
| Serviço vinculado de armazenamento do Azure |Vincula sua conta do Armazenamento do Azure ao data factory. O Armazenamento do Azure é o armazenamento de dados de origem e o Banco de Dados SQL do Azure é o armazenamento de dados do coletor para a atividade de cópia descrita no tutorial. Ele especifica a conta de armazenamento que contém os dados de entrada para a atividade de cópia. |
| Serviço vinculado para o Banco de Dados SQL do Azure |Vincula o Banco de Dados SQL do Azure ao data factory. Especifica o Banco de Dados SQL do Azure que contém os dados de saída para a atividade de cópia. |
| Conjunto de dados de entrada de Blob do Azure |Refere-se ao serviço vinculado do Armazenamento do Azure. O serviço vinculado refere-se a uma conta do Armazenamento do Azure e o conjunto de dados de blob do Azure especifica o contêiner, a pasta e o nome do arquivo no armazenamento que contém os dados de entrada. |
| Conjunto de dados de saída do SQL Azure |Refere-se ao serviço vinculado do SQL do Azure. O serviço vinculado do SQL do Azure refere-se a um SQL Server do Azure e o conjunto de dados do SQL do Azure Especifica o nome da tabela que contém os dados de saída. |
| Pipeline de dados |O pipeline tem uma atividade do tipo Cópia que usa o conjunto de dados de blob do Azure como uma entrada e o conjunto de dados do SQL do Azure como uma saída. A atividade de cópia copia dados de um blob do Azure para uma tabela no Banco de Dados SQL do Azure. |

Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline em um data factory pode ter uma ou mais atividades. Há dois tipos de atividades: [atividades de movimentação de dados](data-factory-data-movement-activities.md) e [atividades de transformação de dados](data-factory-data-transformation-activities.md). Neste tutorial, você criará um pipeline com uma atividade (atividade de cópia).

![Copiar Blob do Azure para o Banco de Dados SQL do Azure](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/CopyBlob2SqlDiagram.png) 

A seção a seguir fornece o modelo do Resource Manager completo para definir entidades de Data Factory de modo que você possa percorrer o tutorial rapidamente e testar o modelo. Para entender como cada entidade de Data Factory é definida, consulte a seção [Entidades de Data Factory no modelo](#data-factory-entities-in-the-template).

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
Crie um arquivo JSON denominado **ADFCopyTutorialARM.json** na pasta **C:\ADFGetStarted** com o seguinte conteúdo:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
      "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the data to be copied." } },
      "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
      "sourceBlobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
      "sourceBlobName": { "type": "string", "metadata": { "description": "Name of the blob in the container that has the data to be copied to Azure SQL Database table" } },
      "sqlServerName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Server that will hold the output/copied data." } },
      "databaseName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Database in the Azure SQL server." } },
      "sqlServerUserName": { "type": "string", "metadata": { "description": "Name of the user that has access to the Azure SQL server." } },
      "sqlServerPassword": { "type": "securestring", "metadata": { "description": "Password for the user." } },
      "targetSQLTable": { "type": "string", "metadata": { "description": "Table in the Azure SQL Database that will hold the copied data." } 
      } 
    },
    "variables": {
      "dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]",
      "azureSqlLinkedServiceName": "AzureSqlLinkedService",
      "azureStorageLinkedServiceName": "AzureStorageLinkedService",
      "blobInputDatasetName": "BlobInputDataset",
      "sqlOutputDatasetName": "SQLOutputDataset",
      "pipelineName": "Blob2SQLPipeline"
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
            "name": "[variables('azureSqlLinkedServiceName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureSqlDatabase",
              "description": "Azure SQL linked service",
              "typeProperties": {
                "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
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
              "structure": [
                {
                  "name": "Column0",
                  "type": "String"
                },
                {
                  "name": "Column1",
                  "type": "String"
                }
              ],
              "typeProperties": {
                "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
                "fileName": "[parameters('sourceBlobName')]",
                "format": {
                  "type": "TextFormat",
                  "columnDelimiter": ","
                }
              },
              "availability": {
                "frequency": "Hour",
                "interval": 1
              },
              "external": true
            }
          },
          {
            "type": "datasets",
            "name": "[variables('sqlOutputDatasetName')]",
            "dependsOn": [
              "[variables('dataFactoryName')]",
              "[variables('azureSqlLinkedServiceName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "type": "AzureSqlTable",
              "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
              "structure": [
                {
                  "name": "FirstName",
                  "type": "String"
                },
                {
                  "name": "LastName",
                  "type": "String"
                }
              ],
              "typeProperties": {
                "tableName": "[parameters('targetSQLTable')]"
              },
              "availability": {
                "frequency": "Hour",
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
              "[variables('azureSqlLinkedServiceName')]",
              "[variables('blobInputDatasetName')]",
              "[variables('sqlOutputDatasetName')]"
            ],
            "apiVersion": "2015-10-01",
            "properties": {
              "activities": [
                {
                  "name": "CopyFromAzureBlobToAzureSQL",
                  "description": "Copy data frm Azure blob to Azure SQL",
                  "type": "Copy",
                  "inputs": [
                    {
                      "name": "[variables('blobInputDatasetName')]"
                    }
                  ],
                  "outputs": [
                    {
                      "name": "[variables('sqlOutputDatasetName')]"
                    }
                  ],
                  "typeProperties": {
                    "source": {
                      "type": "BlobSource"
                    },
                    "sink": {
                      "type": "SqlSink",
                      "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
                    },
                    "translator": {
                      "type": "TabularTranslator",
                      "columnMappings": "Column0:FirstName,Column1:LastName"
                    }
                  },
                  "Policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 3,
                    "timeout": "01:00:00"
                  }
                }
              ],
              "start": "2017-05-11T00:00:00Z",
              "end": "2017-05-12T00:00:00Z"
            }
          }
        ]
      }
    ]
  }
```

## <a name="parameters-json"></a>Parâmetros JSON
Crie um arquivo JSON chamado **ADFCopyTutorialARM-Parameters.json** que contenha os parâmetros para o modelo do Azure Resource Manager. 

> [!IMPORTANT]
> Especifique o nome e a chave de sua conta do Armazenamento do Azure nos parâmetros storageAccountName e storageAccountKey.  
> 
> Especifique o servidor Azure SQL, o banco de dados, o usuário e a senha para os parâmetros sqlServerName, databaseName, sqlServerUserName e sqlServerPassword.  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        "storageAccountName": { "value": "<Name of the Azure storage account>"    },
        "storageAccountKey": {
            "value": "<Key for the Azure storage account>"
        },
        "sourceBlobContainer": { "value": "adftutorial" },
        "sourceBlobName": { "value": "emp.txt" },
        "sqlServerName": { "value": "<Name of the Azure SQL server>" },
        "databaseName": { "value": "<Name of the Azure SQL database>" },
        "sqlServerUserName": { "value": "<Name of the user who has access to the Azure SQL database>" },
        "sqlServerPassword": { "value": "<password for the user>" },
        "targetSQLTable": { "value": "emp" }
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
   * Execute o comando a seguir para selecionar a assinatura com a qual deseja trabalhar.
    
    ```PowerShell
    Get-AzSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzContext
    ```    
2. Execute o comando a seguir para implantar entidades do Data Factory usando o modelo do Resource Manager criado na Etapa 1.

    ```PowerShell   
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFCopyTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFCopyTutorialARM-Parameters.json
    ```

## <a name="monitor-pipeline"></a>Monitorar o pipeline

1. Faça logon no [Portal do Azure](https://portal.azure.com) usando sua conta do Azure.
2. Clique em **Data factories** no menu à esquerda (ou) clique em **Todos os serviços** e clique em **Data factories** na categoria **INTELIGÊNCIA + ANÁLISE**.
   
    ![Menu Data factories](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factories-menu.png)
3. Na página **Data factories**, pesquise e localize o data factory (AzureBlobToAzureSQLDatabaseDF). 
   
    ![Pesquisar por data factory](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/search-for-data-factory.png)  
4. Clique no seu Azure Data Factory. Você verá a home page do data factory.
   
    ![Home page do data factory](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factory-home-page.png)  
6. Siga as instruções de [Monitorar conjuntos de dados e pipeline](data-factory-monitor-manage-pipelines.md) para monitorar o pipeline e os conjuntos de dados criados neste tutorial. Atualmente, o Visual Studio não dá suporte a monitoramento de pipelines do Data Factory.
7. Quando a fatia estiver no estado **Pronto**, verifique se que os dados são copiados para a tabela **emp** no Banco de Dados SQL do Azure.


Para obter instruções sobre como usar as folhas do portal do Azure para monitorar o pipeline e os conjuntos de dados que você criou neste tutorial, confira [Monitorar os conjuntos de dados e o pipeline](data-factory-monitor-manage-pipelines.md).

Para saber mais sobre como usar o aplicativo Monitorar e gerenciar para monitorar os pipelines de dados, confira [Monitorar e gerenciar pipelines do Azure Data Factory usando o aplicativo de monitoramento](data-factory-monitor-manage-app.md).

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
"dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]"
```

É uma cadeia de caracteres exclusiva com base na ID de grupo de recursos.  

### <a name="defining-data-factory-entities"></a>Definir entidades de Data Factory
As seguintes entidades de Data Factory são definidas no modelo JSON: 

1. [Serviço vinculado de armazenamento do Azure](#azure-storage-linked-service)
2. [Serviço vinculado do SQL do Azure](#azure-sql-database-linked-service)
3. [Conjunto de dados de blob do Azure](#azure-blob-dataset)
4. [Conjunto de dados do SQL do Azure](#azure-sql-dataset)
5. [Pipeline de dados com a atividade de cópia](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Serviço vinculado de armazenamento do Azure
O AzureStorageLinkedService vincula sua conta do armazenamento do Azure ao data factory. Você criou um contêiner e carregou dados nessa conta de armazenamento como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). Especifique o nome e a chave da sua conta de armazenamento do Azure nesta seção. Consulte [Serviço vinculado de Armazenamento do Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) para obter detalhes sobre os propriedades JSON usadas para definir um serviço vinculado de armazenamento do Azure. 

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

A connectionString usa os parâmetros storageAccountName e storageAccountKey. Os valores para esses parâmetros são passados pelo uso de um arquivo de configuração. A definição também usa variáveis: azureStorageLinkedService e dataFactoryName definidos no modelo. 

#### <a name="azure-sql-database-linked-service"></a>Serviço vinculado para o Banco de Dados SQL do Azure
O AzureSqlLinkedService vincula seu banco de dados SQL do Azure ao data factory. Os dados copiados do armazenamento de blobs são armazenados no banco de dados. Você criou a tabela emp no banco de dados como parte dos [pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). Especifique o nome do SQL Server do Azure, nome do banco de dados, nome de usuário e senha de usuário nesta seção. Consulte [Serviço vinculado do SQL do Azure](data-factory-azure-sql-connector.md#linked-service-properties) para obter detalhes sobre os propriedades JSON usadas para definir um serviço vinculado do SQL do Azure.  

```json
{
    "type": "linkedservices",
    "name": "[variables('azureSqlLinkedServiceName')]",
    "dependsOn": [
      "[variables('dataFactoryName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "type": "AzureSqlDatabase",
          "description": "Azure SQL linked service",
          "typeProperties": {
            "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
          }
    }
}
```

connectionString usa os parâmetros sqlServerName, databaseName, sqlServerUserName e sqlServerPassword, cujos valores são passados por meio de um arquivo de configuração. A definição também usa as seguintes variáveis do modelo: azureSqlLinkedServiceName, dataFactoryName.

#### <a name="azure-blob-dataset"></a>Conjunto de dados de blob do Azure
O serviço vinculado do Armazenamento do Azure especifica a cadeia de conexão que o serviço Data Factory usa no tempo de execução para se conectar à sua conta do Armazenamento do Azure. Na definição de conjunto de dados do blob do Azure, especifique os nomes do contêiner de blob, da pasta e do arquivo que contém os dados de entrada. Confira [Propriedades de conjunto de dados de Blob do Azure](data-factory-azure-blob-connector.md#dataset-properties) para obter detalhes sobre os propriedades JSON usadas para definir um conjunto de dados de Blob do Azure. 

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
        "structure": [
        {
              "name": "Column0",
              "type": "String"
        },
        {
              "name": "Column1",
              "type": "String"
        }
          ],
          "typeProperties": {
            "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
            "fileName": "[parameters('sourceBlobName')]",
            "format": {
                  "type": "TextFormat",
                  "columnDelimiter": ","
            }
          },
          "availability": {
            "frequency": "Hour",
            "interval": 1
          },
          "external": true
    }
}
```

#### <a name="azure-sql-dataset"></a>Conjunto de dados do SQL do Azure
Você pode especificar o nome da tabela no Banco de Dados SQL do Azure que contém os dados copiados do Armazenamento de Blobs do Azure. Veja [Propriedades de conjunto de dados do SQL do Azure](data-factory-azure-sql-connector.md#dataset-properties) para obter detalhes sobre os propriedades JSON usadas para definir um conjunto de dados do SQL do Azure. 

```json
{
    "type": "datasets",
    "name": "[variables('sqlOutputDatasetName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
          "[variables('azureSqlLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "type": "AzureSqlTable",
          "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
          "structure": [
        {
              "name": "FirstName",
              "type": "String"
        },
        {
              "name": "LastName",
              "type": "String"
        }
          ],
          "typeProperties": {
            "tableName": "[parameters('targetSQLTable')]"
          },
          "availability": {
            "frequency": "Hour",
            "interval": 1
          }
    }
}
```

#### <a name="data-pipeline"></a>Pipeline de dados
Definir um pipeline que copia dados do conjunto de dados de blob do Azure para o conjunto de dados do SQL do Azure. Consulte [JSON de Pipeline](data-factory-create-pipelines.md#pipeline-json) para obter descrições dos elementos JSON usados para definir um pipeline neste exemplo. 

```json
{
    "type": "datapipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
          "[variables('azureStorageLinkedServiceName')]",
          "[variables('azureSqlLinkedServiceName')]",
          "[variables('blobInputDatasetName')]",
          "[variables('sqlOutputDatasetName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
          "activities": [
        {
              "name": "CopyFromAzureBlobToAzureSQL",
              "description": "Copy data frm Azure blob to Azure SQL",
              "type": "Copy",
              "inputs": [
            {
                  "name": "[variables('blobInputDatasetName')]"
            }
              ],
              "outputs": [
            {
                  "name": "[variables('sqlOutputDatasetName')]"
            }
              ],
              "typeProperties": {
                "source": {
                      "type": "BlobSource"
                },
                "sink": {
                      "type": "SqlSink",
                      "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
                },
                "translator": {
                      "type": "TabularTranslator",
                      "columnMappings": "Column0:FirstName,Column1:LastName"
                }
              },
              "Policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 3,
                "timeout": "01:00:00"
              }
        }
          ],
          "start": "2017-05-11T00:00:00Z",
          "end": "2017-05-12T00:00:00Z"
    }
}
```

## <a name="reuse-the-template"></a>Reutilizar o modelo
No tutorial, você criou um modelo para definir entidades de Data Factory e um modelo para passar valores para parâmetros. O pipeline copia dados de uma conta do Armazenamento do Azure para um Banco de Dados SQL do Azure especificado por meio de parâmetros. Para usar o mesmo modelo para implantar as entidades de Data Factory em ambientes diferentes, você cria um arquivo de parâmetro para cada ambiente e usa-o ao implantar esse ambiente.     

Exemplo:  

```PowerShell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Dev.json
```
```PowerShell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Test.json
```
```PowerShell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Production.json
```

Observe que o primeiro comando usa o arquivo de parâmetro para o ambiente de desenvolvimento, outro para o ambiente de teste e um terceiro para o ambiente de produção.  

Também é possível reutilizar o modelo para executar tarefas repetidas. Por exemplo, você precisa criar vários data factories com um ou mais pipelines que implementam a mesma lógica, mas cada data factory usa contas de Armazenamento e do Banco de Dados SQL diferentes. Nesse cenário, você usa o mesmo modelo no mesmo ambiente (desenvolvimento, teste ou produção) com arquivos de parâmetros diferentes para criar data factories.   

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você usou o armazenamento de blobs do Azure como um armazenamento de dados de origem e um banco de dados SQL do Azure como um armazenamento de dados de destino em uma operação de cópia. A tabela a seguir fornece uma lista de armazenamentos de dados com suporte como origens ou destinos na atividade de cópia: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Para saber mais sobre como copiar dados de/para um armazenamento de dados, clique no link para o armazenamento de dados na tabela.
