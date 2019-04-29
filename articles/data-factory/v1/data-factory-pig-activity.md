---
title: Transformar dados usando a Atividade Pig no Azure Data Factory | Microsoft Docs
description: Saiba como usar a Atividade Pig em uma data factory do Azure para executar scripts Pig em um cluster sob demanda/próprio do HDInsight.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 5af07a1a-2087-455e-a67b-a79841b4ada5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 78ee2c1ce402a29f1a9dfdd29f31daef09134eba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611331"
---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>Transformar dados usando a Atividade Pig no Azure Data Factory
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Atividade de Hive](data-factory-hive-activity.md) 
> * [Atividade Pig](data-factory-pig-activity.md)
> * [Atividade MapReduce](data-factory-map-reduce.md)
> * [Atividade de Transmissão do Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade do Spark](data-factory-spark.md)
> * [Atividade de Execução em Lote do Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade do Recurso de Atualização do Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade do U-SQL do Data Lake Analytics](data-factory-usql-activity.md)
> * [Atividade Personalizada do .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [ transformar dados usando a atividade Pig no Data Factory ](../transform-data-using-hadoop-pig.md).


A atividade de Pig do HDInsight em um [pipeline](data-factory-create-pipelines.md) de Data Factory executa consultas de Pig em [seu próprio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) cluster ou no cluster [sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) do HDInsight baseado em Windows/Linux. Este artigo se baseia no artigo sobre [atividades de transformação de dados](data-factory-data-transformation-activities.md) que apresenta uma visão geral da transformação de dados e as atividades de transformação permitidas.

> [!NOTE] 
> Se você é novo no Azure Data Factory, leia a [Introdução ao Azure Data Factory](data-factory-introduction.md) e siga o tutorial: [Criar seu primeiro pipeline de dados](data-factory-build-your-first-pipeline.md) antes de ler este artigo. 

## <a name="syntax"></a>Sintaxe

```JSON
{
  "name": "HiveActivitySamplePipeline",
  "properties": {
    "activities": [
      {
        "name": "Pig Activity",
        "description": "description",
        "type": "HDInsightPig",
        "inputs": [
          {
            "name": "input tables"
          }
        ],
        "outputs": [
          {
            "name": "output tables"
          }
        ],
        "linkedServiceName": "MyHDInsightLinkedService",
        "typeProperties": {
          "script": "Pig script",
          "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
          "defines": {
            "param1": "param1Value"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        }
      }
    ]
  }
}
```

## <a name="syntax-details"></a>Detalhes da sintaxe

| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| nome |Nome da atividade |Sim |
| descrição |Texto que descreve qual a utilidade da atividade |Não |
| Tipo |HDinsightPig |Sim |
| inputs |Uma ou mais entradas consumidas pela atividade Pig |Não  |
| outputs |Uma ou mais saídas produzidas pela atividade Pig |Sim |
| linkedServiceName |Referência ao cluster HDInsight registrado como um serviço vinculado na Data Factory |Sim |
| script |Especificar o script de Pig embutido |Não  |
| caminho do script |Armazenar o script de Pig em um armazenamento de blob do Azure e fornecer o caminho para o arquivo. Use a propriedade 'script' ou 'scriptPath'. As duas não podem ser usadas juntas. O nome do arquivo diferencia maiúsculas de minúsculas. |Não  |
| define |Especificar parâmetros como pares chave/valor para referenciar dentro do script de Pig |Não  |

## <a name="example"></a>Exemplo
Vamos considerar um exemplo de análises de logs de jogos nos quais você deseja identificar o tempo gasto pelos jogadores em jogos lançados por sua empresa.

O log de jogo de exemplo a seguir é um arquivo separado por vírgulas (,). Ele contém os seguintes campos: ProfileID, SessionStart, Duration, SrcIPAddress e GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

O **script Pig** para processar esses dados:

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

Para executar esse script Pig em um pipeline do Data Factory, realize as seguintes etapas:

1. Criar um serviço vinculado para registrar [seu próprio cluster de cálculo HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou configurar o [cluster de cálculo HDInsight sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Vamos chamar esse serviço vinculado de **HDInsightLinkedService**.
2. Criar um [serviço vinculado](data-factory-azure-blob-connector.md) para configurar a conexão com o armazenamento de blob do Azure que está hospedando os dados. Vamos chamar esse serviço vinculado de **StorageLinkedService**.
3. Criar [conjuntos de dados](data-factory-create-datasets.md) apontando para os dados de entrada e de saída. Vamos chamar o conjunto de dados de entrada de **PigSampleIn** e o conjunto de dados de saída de **PigSampleOut**.
4. Copie a consulta Pig em um arquivo do Armazenamento de Blobs do Azure configurado na etapa 2. Se o armazenamento do Azure que hospeda os dados for diferente daquele que hospeda o arquivo de consulta, crie um serviço vinculado do Armazenamento do Azure separado. Confira o serviço vinculado na configuração de atividade. Use **scriptPath** para especificar o caminho para o arquivo de script de pig e **scriptLinkedService**. 
   
   > [!NOTE]
   > Você também pode fornecer o script Pig embutido na definição da atividade usando a propriedade **script** . No entanto, não incentivamos o uso dessa abordagem, pois os caracteres especiais no script precisam de escape e podem causar problemas de depuração. A prática recomendada é seguir a etapa 4.
   >
   >
5. Crie o pipeline com a atividade HDInsightPig. Essa atividade processa os dados de entrada executando o script Pig no cluster HDInsight.

    ```JSON
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
              "scriptLinkedService": "StorageLinkedService"
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            }
          }
        ]
      }
    }
    ```
6. Implante o pipeline. Consulte o artigo [Criando pipelines](data-factory-create-pipelines.md) para obter detalhes. 
7. Monitore o pipeline usando as exibições de gerenciamento e monitoramento de data factory. Consulte o artigo [Monitoramento e gerenciando pipelines do Data Factory](data-factory-monitor-manage-pipelines.md) para obter detalhes.

## <a name="specifying-parameters-for-a-pig-script"></a>Especificando parâmetros para um script Pig
Considere o seguinte exemplo: os logs de jogos são ingeridos diariamente no Armazenamento de Blobs do Azure e armazenados em uma pasta particionada com base na data e na hora. Convém parametrizar o script Pig e passar o local da pasta entrada dinamicamente durante a execução, além de produzir a saída particionada com data e hora.

Para usar os scripts Pig parametrizado, faça o seguinte:

* Defina os parâmetros em **defines**.

    ```JSON
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
              "scriptLinkedService": "StorageLinkedService",
              "defines": {
                "Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0:MM}/dayno={0: dd}/',SliceStart)",
                "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
              }
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            }
          }
        ]
      }
    }
    ```
* No Script Pig, consulte os parâmetros usando '**$parameterName**', como mostra o exemplo a seguir:

    ```
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    GroupProfile = Group PigSampleIn all;
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```

## <a name="see-also"></a>Veja também
* [Atividade de Hive](data-factory-hive-activity.md)
* [Atividade MapReduce](data-factory-map-reduce.md)
* [Atividade de Transmissão do Hadoop](data-factory-hadoop-streaming-activity.md)
* [Invocar programas Spark](data-factory-spark.md)
* [Invocar scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)
