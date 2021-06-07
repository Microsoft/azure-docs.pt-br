---
title: Carregar um conjunto de dados grandes em métodos Azure Data Lake Storage Gen1-offline
description: Use o serviço de importação/exportação para copiar dados do armazenamento de BLOBs do Azure para Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 940b7ac90f85e0254d59459b70ccc15312cd69f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98700832"
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-data-lake-storage-gen1"></a>Usar o serviço de importação/exportação do Azure para cópia offline de dados para Data Lake Storage Gen1

Neste artigo, você aprenderá a copiar grandes conjuntos de dados (>200 GB) para Data Lake Storage Gen1 usando métodos de cópia offline, como o [serviço de importação/exportação do Azure](../import-export/storage-import-export-service.md). Especificamente, o arquivo usado como exemplo nesse artigo tem 339.420.860.416 bytes, ou aproximadamente 319 GB em disco. Vamos chamar esse arquivo de 319GB.tsv.

O Serviço de Importação/Exportação do Azure ajuda você a transferir com segurança grandes quantidades de dados para o Armazenamento de Blobs do Azure por meio do envio de unidades de disco rígido para um data center do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deverá ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta de armazenamento do Azure**.
* **Uma conta do Azure Data Lake Storage Gen1**. Para obter instruções de como criar uma, confira [Introdução ao Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

## <a name="prepare-the-data"></a>Preparar os dados

Antes de usar o serviço de Importação/Exportação, divida o arquivo de dados a ser transferido **em cópias de menos de 200 GB** de tamanho. A ferramenta de importação não funciona em arquivos com mais de 200 GB. Neste artigo, dividimos o arquivo em partes de 100 GB cada. Você pode fazer isso usando o [Cygwin](https://cygwin.com/install.html). O Cygwin dá suporte a comandos do Linux. Nesse caso, use o seguinte comando:

```console
split -b 100m 319GB.tsv
```

A operação de divisão cria arquivos com os nomes a seguir.

* *319GB. tsv-parte-AA*
* *319GB. tsv-Part-AB*
* *319GB. tsv-parte-AC*
* *319GB. tsv-parte-AD*

## <a name="get-disks-ready-with-data"></a>Prepare os discos com dados

Siga as instruções em [Usando o serviço de Importação/Exportação do Azure](../import-export/storage-import-export-service.md) (na seção **Preparar suas unidades**) para preparar seus discos rígidos. Aqui está a sequência geral:

1. Compre um disco rígido que atenda ao requisito para ser usado no serviço de Importação/Exportação do Azure.
2. Identifique uma conta de armazenamento do Azure na qual os dados serão copiados depois que forem enviados para o data center do Azure.
3. Use a [Ferramenta de Importação/Exportação do Azure](https://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), um utilitário de linha de comando. Aqui está um snippet de código que mostra como usar a ferramenta.

    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ```
    Confira [Using the Azure Import/Export service](../import-export/storage-import-export-service.md) (Usando o serviço de Importação/Exportação do Azure) para obter mais snippets de código de exemplo.
4. O comando anterior cria um arquivo de diário no local especificado. Use esse arquivo de diário para criar um trabalho de importação do [Portal do Azure](https://portal.azure.com).

## <a name="create-an-import-job"></a>Criar um trabalho de importação

Agora, você pode criar um trabalho de importação usando as instruções em [Using the Azure Import/Export service](../import-export/storage-import-export-service.md) (Usando o serviço de Importação/Exportação do Azure) (na seção **Criar o trabalho de importação**). Para este trabalho de importação, com outros detalhes, também fornece o arquivo de diário criado durante a preparação de unidades de disco.

## <a name="physically-ship-the-disks"></a>Enviar fisicamente os discos

Agora, você pode enviar fisicamente os discos para um datacenter do Azure. Lá, os dados são copiados nos Azure Storage Blobs que você forneceu ao criar o trabalho de importação. Além disso, ao criar o trabalho, se tiver optado por fornecer as informações de acompanhamento posteriormente, agora você poderá voltar ao trabalho de importação e atualizar o número de controle.

## <a name="copy-data-from-blobs-to-data-lake-storage-gen1"></a>Copiar dados de BLOBs para Data Lake Storage Gen1

Depois que o status do trabalho de importação tiver sido concluído, você poderá verificar se os dados estão disponíveis nos Azure Storage Blobs especificados. Em seguida, você poderá usar uma variedade de métodos para mover esses dados dos blobs para o Azure Data Lake Storage Gen1. Para todas as opções disponíveis para carregamento de dados, confira [Ingerindo dados no Data Lake Storage Gen1](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-storage-gen1).

Nesta seção, fornecemos as definições de JSON que você pode usar para criar um pipeline do Azure Data Factory para copiar dados. Você pode usar essas definições de JSON do [portal do Azure](../data-factory/tutorial-copy-data-portal.md) ou do [Visual Studio](../data-factory/tutorial-copy-data-dot-net.md).

### <a name="source-linked-service-azure-storage-blob"></a>Serviço vinculado de origem (Azure Storage Blob)

```JSON
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="target-linked-service-data-lake-storage-gen1"></a>Serviço vinculado de destino (Data Lake Storage Gen1)

```JSON
{
    "name": "AzureDataLakeStorageGen1LinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Storage Gen1 account with your access rights>",
            "dataLakeStoreUri": "https://<adlsg1_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
```

### <a name="input-data-set"></a>Conjunto de dados de entrada

```JSON
{
    "name": "InputDataSet",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "importcontainer/vf1/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

### <a name="output-data-set"></a>Conjunto de dados de saída

```JSON
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStorageGen1LinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
```

### <a name="pipeline-copy-activity"></a>Pipeline (atividade de cópia)

```JSON
{
    "name": "CopyImportedData",
    "properties": {
        "description": "Pipeline with copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity"
            }
        ],
        "start": "2016-02-08T22:00:00Z",
        "end": "2016-02-08T23:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

Para obter mais informações, confira [Mover dados do Azure Storage Blob para o Azure Data Lake Storage Gen1 usando o Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="reconstruct-the-data-files-in-data-lake-storage-gen1"></a>Reconstruir os arquivos de dados no Data Lake Storage Gen1

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Começamos com um arquivo que tinha 319 GB e o dividimos em arquivos de tamanho menor, para que eles pudessem ser transferidos usando o serviço de Importação/Exportação do Azure. Agora que os dados estão no Azure Data Lake Storage Gen1, podemos reconstruir o arquivo para o seu tamanho original. Você pode usar os cmdlets do Azure PowerShell a seguir para fazer isso.

```PowerShell
# Login to our account
Connect-AzAccount

# List your subscriptions
Get-AzSubscription

# Switch to the subscription you want to work with
Set-AzContext -SubscriptionId
Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzDataLakeStoreItem -AccountName "<adlsg1_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv"
```

## <a name="next-steps"></a>Próximas etapas

* [Proteger dados no Armazenamento do Data Lake Gen1](data-lake-store-secure-data.md)
* [Usar o Azure Data Lake Analytics com o Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usar o Azure HDInsight com o Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
