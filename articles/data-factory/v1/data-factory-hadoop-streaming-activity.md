---
title: Transformar dados usando a atividade de streaming do Hadoop – Azure
description: Saiba como usar a Atividade de Streaming do Hadoop em um Azure Data Factory para transformar dados executando programas de Streaming do Hadoop em um cluster HDInsight sob demanda ou em seu próprio cluster HDInsight.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.assetid: 4c3ff8f2-2c00-434e-a416-06dfca2c41ec
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 80f78e3d2e7fdcd8fef53ca0412676a37c6486c2
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96495592"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Transformar dados usando a Atividade de Streaming do Hadoop no Azure Data Factory
> [!div class="op_single_selector" title1="Atividades de transformação"]
> * [Atividade do hive](data-factory-hive-activity.md) 
> * [Atividade Pig](data-factory-pig-activity.md)
> * [Atividade MapReduce](data-factory-map-reduce.md)
> * [Atividade de streaming do Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade do Spark](data-factory-spark.md)
> * [Atividade de execução em lotes do Azure Machine Learning Studio (clássico)](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade do recurso de atualização do Azure Machine Learning Studio (clássico)](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade do U-SQL da Análise Data Lake](data-factory-usql-activity.md)
> * [Atividade personalizada do .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [transformar dados usando o Hadoop Streaming Activity no Data Factory](../transform-data-using-hadoop-streaming.md).


Você pode usar a atividade HDInsightStreamingActivity para invocar um trabalho de Transmissão do Hadoop de um pipeline do Azure Data Factory. O snippet de código JSON a seguir mostra a sintaxe para usar HDInsightStreamingActivity em um arquivo JSON do pipeline. 

A atividade de streaming do HDInsight em um [pipeline](data-factory-create-pipelines.md) data Factory executa programas de streaming do Hadoop em [seu próprio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) cluster do HDInsight baseado em Windows/Linux [sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . Este artigo se baseia no artigo sobre [atividades de transformação de dados](data-factory-data-transformation-activities.md) que apresenta uma visão geral da transformação de dados e as atividades de transformação permitidas.

> [!NOTE] 
> Se você estiver conhecendo o Azure Data Factory agora, leia a [Introdução ao Azure Data Factory](data-factory-introduction.md) e siga o tutorial [Criar seu primeiro pipeline de dados](data-factory-build-your-first-pipeline.md) antes de ler este artigo. 

## <a name="json-sample"></a>Exemplo de JSON
O cluster HDInsight é preenchido automaticamente com os programas de exemplo (wc.exe e cat.exe) e os dados (DaVinci). Por padrão, o nome do contêiner que é usado pelo cluster HDInsight é o nome do próprio cluster. Por exemplo, se o nome do cluster for myhdicluster, o nome do contêiner de blob associado seria myhdicluster. 

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<nameofthecluster>/example/apps/wc.exe",
                        "<nameofthecluster>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "AzureStorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00Z",
        "end": "2014-01-05T00:00:00Z"
    }
}
```

Observe os seguintes pontos:

1. Defina **linkedServiceName** para o nome do serviço vinculado que aponta para o cluster do HDInsight no qual o trabalho do MapReduce de transmissão é executado.
2. Defina o tipo da atividade como **HDInsightStreaming**.
3. Para a propriedade **mapeador**, especifique o nome do executável do mapeador. No exemplo, cat.exe é o executável do mapeador.
4. Para a propriedade **reducer** , especifique o nome do executável do redutor. No exemplo, wc.exe é o executável do redutor.
5. Para a propriedade de tipo **input** , especifique o arquivo de entrada (incluindo o local) para o mapeador. No exemplo: `wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt`: adfsample é o contêiner de blob, example/data/Gutenberg é a pasta e davinci.txt é o blob.
6. Para a propriedade de tipo **output** , especifique o arquivo de saída (incluindo o local) para o redutor. A saída do trabalho de Transmissão do Hadoop é gravada no local especificado para essa propriedade.
7. Na seção **filePaths**, especifique os caminhos dos executáveis do mapeador e redutor. No exemplo: "adfsample/example/apps/wc.exe", adfsample é o contêiner de blob, example/apps é a pasta e wc.exe é o executável.
8. Para a propriedade **fileLinkedService** , especifique o serviço vinculado do Armazenamento do Azure que representa o armazenamento do Azure que contém os arquivos especificados na seção filePaths.
9. Para a propriedade **argumentos**, especifique os argumentos do trabalho de transmissão.
10. A propriedade **getDebugInfo** é um elemento opcional. Quando ela é definida como Falha, os logs são baixados somente em caso de falha de execução. Quando ela é definida como Sempre, os logs sempre são baixados, não importa o status de execução.

> [!NOTE]
> Como mostrado no exemplo, você especifica um conjunto de dados de saída da Atividade de Transmissão do Hadoop para a propriedade **outputs** . Esse conjunto de dados é apenas um conjunto fictício exigido para direcionar a agenda de pipeline. Você não precisa especificar qualquer conjunto de dados de entrada para a atividade da propriedade **entradas** .  
> 
> 

## <a name="example"></a>Exemplo
O pipeline neste passo a passo executa o programa de mapa/redução de streaming de contagem de palavras no cluster do HDInsight do Azure. 

### <a name="linked-services"></a>Serviços vinculados
#### <a name="azure-storage-linked-service"></a>Serviço vinculado de armazenamento do Azure
Primeiro, crie um serviço vinculado para vincular o armazenamento do Azure que é usado pelo cluster do Azure HDInsight à fábrica de dados do Azure. Se você copiar/colar o código a seguir, não se esqueça de substituir o nome da conta e a chave de conta pelo nome e chave do armazenamento do Azure. 

```JSON
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

#### <a name="azure-hdinsight-linked-service"></a>Serviço vinculado do Azure HDInsight
Em seguida, você cria um serviço vinculado para vincular seu cluster do HDInsight do Azure para a fábrica de dados do Azure. Se você copiar/colar o código a seguir, substitua o nome do cluster do HDInsight pelo nome do seu cluster do HDInsight e altere os valores de nome e senha do usuário. 

```JSON
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
            "userName": "admin",
            "password": "**********",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

### <a name="datasets"></a>Conjuntos de dados
#### <a name="output-dataset"></a>Conjunto de dados de saída
O pipeline neste exemplo não tem entradas. Você especifica um conjunto de dados de saída para a Atividade de Transmissão do HDInsight. Esse conjunto de dados é apenas um conjunto fictício exigido para direcionar a agenda de pipeline. 

```JSON
{
    "name": "StreamingOutputDataset",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "adftutorial/streamingdata/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>Pipeline
O pipeline neste exemplo tem apenas uma atividade que seja do tipo: **HDInsightStreaming**. 

O cluster HDInsight é preenchido automaticamente com os programas de exemplo (wc.exe e cat.exe) e os dados (DaVinci). Por padrão, o nome do contêiner que é usado pelo cluster HDInsight é o nome do próprio cluster. Por exemplo, se o nome do cluster for myhdicluster, o nome do contêiner de blob associado seria myhdicluster.  

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<blobcontainer>/example/apps/wc.exe",
                        "<blobcontainer>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "StorageLinkedService"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00Z",
        "end": "2017-01-04T00:00:00Z"
    }
}
```
## <a name="see-also"></a>Consulte Também
* [Atividade do hive](data-factory-hive-activity.md)
* [Atividade Pig](data-factory-pig-activity.md)
* [Atividade MapReduce](data-factory-map-reduce.md)
* [Invocar programas Spark](data-factory-spark.md)
* [Invocar scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

