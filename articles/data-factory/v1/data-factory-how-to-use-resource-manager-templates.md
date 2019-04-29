---
title: Usar modelos do Resource Manager no Data Factory | Microsoft Docs
description: Saiba como criar e usar modelos do Azure Resource Manager para criar entidades de Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.assetid: 37724021-f55f-4e85-9206-6d4a48bda3d8
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: ca8b3930b9d9f708d83dc760be3ee89737b074dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60583359"
---
# <a name="use-templates-to-create-azure-data-factory-entities"></a>Usar modelos para criar entidades do Azure Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. 

## <a name="overview"></a>Visão geral
Ao usar o Azure Data Factory para suas necessidades de integração de dados, talvez você precise reutilizar o mesmo padrão em diversos ambientes ou implementar a mesma tarefa repetidamente na mesma solução. Modelos ajudam você a implementar e gerenciar estes cenários de forma fácil. Modelos no Azure Data Factory são ideais para cenários que envolvem reutilização e repetição.

Considere a situação em que uma organização tem 10 instalações de produção em todo o mundo. Os logs de cada fábrica são armazenados em um banco de dados do SQL Server local separado. A empresa quer criar um único data warehouse na nuvem para análise ad hoc. Ele também quer ter a mesma lógica, mas configurações diferentes para ambientes de desenvolvimento, teste e produção.

Nesse caso, uma tarefa precisa ser repetida dentro do mesmo ambiente, mas com valores diferentes entre as 10 fábricas de dados para cada fábrica. Na verdade, **repetição** está presente. A modelagem permite a abstração desse fluxo genérico (ou seja, pipelines com as mesmas atividades em cada data factory), mas usa um arquivo de parâmetro separado para cada fábrica.

Além disso, como a organização deseja implantar essas 10 data factories diversas vezes em vários ambientes, os modelos podem usar a **reutilização** ao utilizar arquivos de parâmetros separados para ambientes de desenvolvimento, teste e produção.

## <a name="templating-with-azure-resource-manager"></a>Modelagem com o Azure Resource Manager
[Modelos do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md#template-deployment) são uma ótima maneira de obter modelagem no Azure Data Factory. Os modelos do Resource Manager definem a infraestrutura e a configuração de sua solução do Azure por meio de um arquivo JSON. Como modelos de Azure Resource Manager funcionam com todos/quase todos os serviços do Azure, eles podem ser amplamente usados para gerenciar facilmente todos os recursos dos ativos do Azure. Consulte [Criando modelos do Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) para saber mais sobre os modelos do Resource Manager em geral.

## <a name="tutorials"></a>Tutoriais
Consulte os tutoriais a seguir para obter instruções passo a passo para criar entidades de Data Factory usando modelos do Resource Manager:

* [Tutorial: Criar um pipeline para copiar os dados usando o modelo do Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [Tutorial: Criar um pipeline para processar os dados usando o modelo do Azure Resource Manager](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Modelos do Data Factory no GitHub
Confira os seguintes modelos de início rápido do Azure no GitHub:

* [Criar o Data Factory para copiar dados do Armazenamento de Blobs do Azure para o Banco de Dados SQL do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
* [Criar um Data Factory com a atividade do Hive em um cluster HDInsight do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
* [Criar um Data Factory para copiar dados do Salesforce para Blobs do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)
* [Criar um Data Factory que encadeia atividades: copia dados de um servidor FTP para Blobs do Azure, invoca um script hive em um cluster HDInsight sob demanda para transformar os dados e copia o resultado no Banco de Dados SQL do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-data-factory-ftp-hive-blob)

Fique à vontade para compartilhar seus modelos do Azure Data Factory em [início rápido do Azure](https://azure.microsoft.com/documentation/templates/). Consulte o [guia de contribuição](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) ao desenvolver modelos que podem ser compartilhados por meio deste repositório.

As seções a seguir fornecem detalhes sobre como definir recursos do Data Factory em um modelo do Resource Manager.

## <a name="defining-data-factory-resources-in-templates"></a>Definir recursos de Data Factory em modelos
O modelo de nível superior para definir um Data Factory é:

```JSON
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
    { "type": "linkedservices",
        ...
    },
    {"type": "datasets",
        ...
    },
    {"type": "dataPipelines",
        ...
    }
}
```

### <a name="define-data-factory"></a>Definir Data Factory
Você pode definir um Data Factory no modelo do Resource Manager, conforme mostrado no exemplo a seguir:

```JSON
"resources": [
{
    "name": "[variables('<mydataFactoryName>')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "East US"
}
```
O dataFactoryName é definido em “variáveis” como:

```JSON
"dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",
```

### <a name="define-linked-services"></a>Definir serviços vinculados

```JSON
"type": "linkedservices",
"name": "[variables('<LinkedServiceName>')]",
"apiVersion": "2015-10-01",
"dependsOn": [ "[variables('<dataFactoryName>')]" ],
"properties": {
    ...
}
```

Consulte [Serviço de Armazenamento Vinculado](data-factory-azure-blob-connector.md#azure-storage-linked-service) ou [Serviços de Computação Vinculados](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes sobre as propriedades JSON para o serviço vinculado específico que você deseja implantar. O parâmetro "dependsOn" especifica o nome do Data Factory correspondente. Um exemplo de definição de um serviço vinculado do Armazenamento do Azure é mostrado na definição de JSON a seguir:

### <a name="define-datasets"></a>Definir conjuntos de dados

```JSON
"type": "datasets",
"name": "[variables('<myDatasetName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<myDatasetLinkedServiceName>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    ...
}
```
Consulte [Armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) para obter detalhes sobre as propriedades JSON para o tipo de conjunto de dados específico que você deseja implantar. Observe que o parâmetro "dependsOn" especifica o nome do Data Factory correspondente e o serviço de armazenamento vinculado. Um exemplo de definição de um tipo de conjunto de dados do armazenamento de blobs do Azure na seguinte definição JSON:

```JSON
"type": "datasets",
"name": "[variables('storageDataset')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('storageLinkedServiceName')]"
],
"apiVersion": "2015-10-01",
"properties": {
"type": "AzureBlob",
"linkedServiceName": "[variables('storageLinkedServiceName')]",
"typeProperties": {
    "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
    "fileName": "[parameters('sourceBlobName')]",
    "format": {
        "type": "TextFormat"
    }
},
"availability": {
    "frequency": "Hour",
    "interval": 1
}
```

### <a name="define-pipelines"></a>Definir pipelines

```JSON
"type": "dataPipelines",
"name": "[variables('<mypipelineName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<inputDatasetLinkedServiceName>')]",
    "[variables('<outputDatasetLinkedServiceName>')]",
    "[variables('<inputDataset>')]",
    "[variables('<outputDataset>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    activities: {
        ...
    }
}
```

Consulte [definindo pipelines](data-factory-create-pipelines.md#pipeline-json) para obter detalhes sobre as propriedades JSON para definir o pipeline específico e as atividades que você deseja implantar. Observe o parâmetro "dependsOn" especifica o nome do Data Factory e quaisquer serviços vinculados ou conjuntos de dados correspondentes. Um exemplo de um pipeline que copia dados do Armazenamento de Blobs do Azure para o Banco de Dados SQL do Azure é mostrado no seguinte snippet de JSON:

```JSON
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
    "start": "2016-10-03T00:00:00Z",
    "end": "2016-10-04T00:00:00Z"
}
```
## <a name="parameterizing-data-factory-template"></a>Parametrizando o modelo de Data Factory
Para obter as práticas recomendadas sobre parametrização, consulte [Práticas recomendadas para criar modelos do Azure Resource Manager](../../azure-resource-manager/resource-manager-template-best-practices.md). Em geral, o uso do parâmetro deverá ser minimizado, especialmente se variáveis puderem ser usadas em vez disso. Apenas forneça parâmetros nos seguintes cenários:

* As configurações variam de acordo com ambiente (exemplo: desenvolvimento, teste e produção)
* Segredos (por exemplo, senhas)

Se você precisar receber segredos do [Cofre de Chaves do Azure](../../key-vault/key-vault-overview.md) ao implantar entidades do Azure Data Factory usando modelos, especifique o **cofre de chaves** e **nome secreto** conforme mostrado no exemplo a seguir:

```JSON
"parameters": {
    "storageAccountKey": {
        "reference": {
            "keyVault": {
                "id":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.KeyVault/vaults/<keyVaultName>",
             },
            "secretName": "<secretName>"
           },
       },
       ...
}
```

> [!NOTE]
> Embora ainda não haja suporte para exportar modelos para Data Factories existentes, ele ainda funciona.
>
>
