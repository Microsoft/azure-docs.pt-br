---
title: "Carregar grandes quantidades de dados no Data Lake Store usando métodos offline | Microsoft Docs"
description: Usar a ferramenta AdlCopy para copiar dados dos Azure Storage Blobs para o Data Lake Store
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 45321f6a-179f-4ee4-b8aa-efa7745b8eb6
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 1309b44ea99af6d20a4d0f730dd68969f3c3082b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-data-lake-store"></a>Usar o Serviço de Importação/Exportação do Azure para uma cópia offline dos dados para o Data Lake Store
Nesse artigo, você aprenderá a copiar grandes conjuntos de dados (> 200 GB) para um Azure Data Lake Store usando métodos de cópia offline, como o [Serviço de Importação/Exportação do Azure](../storage/common/storage-import-export-service.md). Especificamente, o arquivo usado como exemplo nesse artigo tem 339.420.860.416 bytes, ou aproximadamente 319 GB em disco. Vamos chamar esse arquivo de 319GB.tsv.

O Serviço de Importação/Exportação do Azure ajuda você a transferir com segurança grandes quantidades de dados para o Armazenamento de Blobs do Azure por meio do envio de unidades de disco rígido para um data center do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, você deverá ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta de armazenamento do Azure**.
* **Uma conta do repositório Azure Data Lake**. Para obter instruções sobre como criar uma, consulte [Introdução ao repositório Azure Data Lake](data-lake-store-get-started-portal.md)

## <a name="preparing-the-data"></a>Preparando os dados
Antes de usar o serviço de Importação/Exportação, divida o arquivo de dados a ser transferido **em cópias de menos de 200 GB** de tamanho. A ferramenta de importação não funciona em arquivos com mais de 200 GB. Nesse tutorial, vamos dividir o arquivo em blocos de 100 GB. Você pode fazer isso usando o [Cygwin](https://cygwin.com/install.html). O Cygwin dá suporte a comandos do Linux. Nesse caso, use o seguinte comando:

    split -b 100m 319GB.tsv

A operação de divisão cria arquivos com os nomes a seguir.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Prepare os discos com dados
Siga as instruções em [Usando o serviço de Importação/Exportação do Azure](../storage/common/storage-import-export-service.md) (na seção **Preparar suas unidades**) para preparar seus discos rígidos. Aqui está a sequência geral:

1. Compre um disco rígido que atenda ao requisito para ser usado no serviço de Importação/Exportação do Azure.
2. Identifique uma conta de armazenamento do Azure na qual os dados serão copiados depois que forem enviados para o data center do Azure.
3. Use a [Ferramenta de Importação/Exportação do Azure](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), um utilitário de linha de comando. Aqui está um trecho de código que mostra como usar a ferramenta.

    ````
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ````
    Confira [Using the Azure Import/Export service](../storage/common/storage-import-export-service.md) (Usando o serviço de Importação/Exportação do Azure) para obter mais trechos de código de exemplo.
4. O comando anterior cria um arquivo de diário no local especificado. Use esse arquivo de diário para criar um trabalho de importação do [Portal Clássico do Azure](https://manage.windowsazure.com).

## <a name="create-an-import-job"></a>Criar um trabalho de importação
Agora, você pode criar um trabalho de importação usando as instruções em [Using the Azure Import/Export service](../storage/common/storage-import-export-service.md) (Usando o serviço de Importação/Exportação do Azure) (na seção **Criar o trabalho de importação**). Para este trabalho de importação, com outros detalhes, também fornece o arquivo de diário criado durante a preparação de unidades de disco.

## <a name="physically-ship-the-disks"></a>Enviar fisicamente os discos
Agora, você pode enviar fisicamente os discos para um datacenter do Azure. Lá, os dados são copiados nos Azure Storage Blobs que você forneceu ao criar o trabalho de importação. Além disso, ao criar o trabalho, se tiver optado por fornecer as informações de acompanhamento posteriormente, agora você poderá voltar ao trabalho de importação e atualizar o número de controle.

## <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-store"></a>Copiar dados dos Azure Storage Blobs para o Azure Data Lake Store
Depois que o status do trabalho de importação tiver sido concluído, você poderá verificar se os dados estão disponíveis nos Azure Storage Blobs especificados. Em seguida, você poderá usar uma variedade de métodos para mover esses dados dos blobs para o Azure Data Lake Store. Para todas as opções disponíveis para carregamento de dados, consulte [Ingerindo dados no Data Lake Store](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-store).

Nesta seção, fornecemos as definições de JSON que você pode usar para criar um pipeline do Azure Data Factory para copiar dados. Você pode usar essas definições de JSON do [Portal do Azure](../data-factory/v1/data-factory-copy-activity-tutorial-using-azure-portal.md), do [Visual Studio](../data-factory/v1/data-factory-copy-activity-tutorial-using-visual-studio.md) ou do [Azure PowerShell](../data-factory/v1/data-factory-copy-activity-tutorial-using-powershell.md).

### <a name="source-linked-service-azure-storage-blob"></a>Serviço vinculado de origem (Azure Storage Blob)
````
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
````

### <a name="target-linked-service-azure-data-lake-store"></a>Serviço vinculado de destino (Azure Data Lake Store)
````
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Store with your access rights>",
            "dataLakeStoreUri": "https://<adls_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
````
### <a name="input-data-set"></a>Conjunto de dados de entrada
````
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
````
### <a name="output-data-set"></a>Conjunto de dados de saída
````
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStoreLinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
````
### <a name="pipeline-copy-activity"></a>Pipeline (atividade de cópia)
````
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
````
Para obter mais informações, confira [Move data from Azure Storage blob to Azure Data Lake Store using Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) (Mover dados do Azure Storage Blob para o Azure Data Lake Store usando o Azure Data Factory).

## <a name="reconstruct-the-data-files-in-azure-data-lake-store"></a>Reconstruir os arquivos de dados no Azure Data Lake Store
Começamos com um arquivo que tinha 319 GB e o dividimos em arquivos de tamanho menor, para que eles pudessem ser transferidos usando o serviço de Importação/Exportação do Azure. Agora que os dados estão no Azure Data Lake Store, podemos reconstruir o arquivo para o seu tamanho original. Você pode usar os seguintes cmdlets do Azure PowerShell para fazer isso.

````
# Login to our account
Login-AzureRmAccount

# List your subscriptions
Get-AzureRmSubscription

# Switch to the subscription you want to work with
Set-AzureRmContext –SubscriptionId
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzureRmDataLakeStoreItem -AccountName "<adls_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv”
````

## <a name="next-steps"></a>Próximas etapas
* [Proteger dados no Repositório Data Lake](data-lake-store-secure-data.md)
* [Usar a Análise Data Lake do Azure com o Repositório Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usar o Azure HDInsight com o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
