---
title: Transformar dados usando a Atividade de Hive - Azure | Microsoft Docs
description: Saiba como usar a atividade de Hive em uma Azure Data Factory para executar consultas de Hive em um cluster sob demanda/próprio de HDInsight.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 80083218-743e-4da8-bdd2-60d1c77b1227
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: a63ef969f17fc48145174d99fec53e77b61885a4
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827986"
---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>Transformar dados usando a Atividade de Hive no Azure Data Factory 
> [!div class="op_single_selector" title1="Atividades de transformação"]
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
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [ transformar dados usando a atividade Hive no Data Factory](../transform-data-using-hadoop-hive.md).

A atividade de Hive do HDInsight em um [pipeline](data-factory-create-pipelines.md) do Data Factory executa consultas de Hive em [seu próprio cluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou no [cluster sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) do HDInsight baseado em Windows/Linux. Este artigo se baseia no artigo sobre [atividades de transformação de dados](data-factory-data-transformation-activities.md) que apresenta uma visão geral da transformação de dados e as atividades de transformação permitidas.

> [!NOTE] 
> Se você é novo no Azure Data Factory, leia a [Introdução ao Azure Data Factory](data-factory-introduction.md) e siga o tutorial: [Criar seu primeiro pipeline de dados](data-factory-build-your-first-pipeline.md) antes de ler este artigo. 

## <a name="syntax"></a>Sintaxe

```JSON
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
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
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```
## <a name="syntax-details"></a>Detalhes da sintaxe
| Propriedade | DESCRIÇÃO | Obrigatório |
| --- | --- | --- |
| name |Nome da atividade |Sim |
| description |Texto que descreve qual a utilidade da atividade |Não |
| type |HDInsightHive |Sim |
| inputs |Entradas consumidas pela atividade de Hive |Não |
| outputs |Saídas produzidas pela atividade de Hive |Sim |
| linkedServiceName |Referência ao cluster HDInsight registrado como um serviço vinculado na Data Factory |Sim |
| script |Especifique o script de Hive embutido |Não |
| scriptPath |Armazene o script de Hive em um armazenamento de blob do Azure e forneça o caminho para o arquivo. Use a propriedade 'script' ou 'scriptPath'. As duas não podem ser usadas juntas. O nome do arquivo diferencia maiúsculas de minúsculas. |Não |
| defines |Especifique parâmetros como pares chave/valor para referenciar dentro do script de Hive usando 'hiveconf' |Não |

## <a name="example"></a>Exemplo
Vamos considerar um exemplo de análises de logs de jogos nos quais você deseja identificar o tempo gasto pelos usuários em jogos lançados por sua empresa. 

O log a seguir é um log de jogo de exemplo, que é separado por vírgula (`,`) e contém os seguintes campos – ProfileID, SessionStart, Duration, SrcIPAddress e GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

O **script do Hive** para processar esses dados:

```
DROP TABLE IF EXISTS HiveSampleIn; 
CREATE EXTERNAL TABLE HiveSampleIn 
(
    ProfileID        string, 
    SessionStart     string, 
    Duration         int, 
    SrcIPAddress     string, 
    GameType         string
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 

DROP TABLE IF EXISTS HiveSampleOut; 
CREATE EXTERNAL TABLE HiveSampleOut 
(    
    ProfileID     string, 
    Duration     int
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';

INSERT OVERWRITE TABLE HiveSampleOut
Select 
    ProfileID,
    SUM(Duration)
FROM HiveSampleIn Group by ProfileID
```

Para executar o script do Hive em um pipeline do Data Factory, você precisa fazer o seguinte

1. Criar um serviço vinculado para registrar [seu próprio cluster de cálculo HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou configurar o [cluster de cálculo HDInsight sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Vamos chamar esse serviço vinculado de "HDInsightLinkedService".
2. Criar um [serviço vinculado](data-factory-azure-blob-connector.md) para configurar a conexão com o Armazenamento de Blob do Azure que está hospedando os dados. Vamos chamar esse serviço vinculado de "StorageLinkedService".
3. Criar [conjuntos de dados](data-factory-create-datasets.md) apontando para os dados de entrada e de saída. Vamos chamar o conjunto de dados de entrada de "HiveSampleIn" e o conjunto de dados de saída de "HiveSampleOut"
4. Copie a consulta do Hive como um arquivo para o Armazenamento de Blobs do Azure configurado na etapa 2. Se o armazenamento para hospedar os dados for diferente daquele que hospeda o arquivo de consulta, crie um serviço vinculado do Armazenamento do Azure separado e refira-o na atividade. Use **scriptPath** para especificar o caminho para o arquivo de consulta do Hive e **scriptLinkedService** para especificar o armazenamento do Azure que contém o arquivo de script. 
   
   > [!NOTE]
   > Você também pode fornecer o script Hive embutido na definição da atividade usando a propriedade **script** . Não recomendamos essa abordagem, pois os caracteres especiais no script no documento JSON precisam de escape e podem causar problemas de depuração. A prática recomendada é seguir a etapa 4.
   > 
   > 
5. Crie um pipeline com a atividade HDInsightHive. A atividade processa/transforma os dados.

    ```JSON   
    {   
        "name": "HiveActivitySamplePipeline",
        "properties": {
        "activities": [
            {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                {
                    "name": "HiveSampleIn"
                }
                ],
                "outputs": [
                {
                    "name": "HiveSampleOut"
                }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
            ]
        }
    }
    ```
6. Implante o pipeline. Consulte o artigo [Criando pipelines](data-factory-create-pipelines.md) para obter detalhes. 
7. Monitore o pipeline usando as exibições de gerenciamento e monitoramento de data factory. Consulte o artigo [Monitoramento e gerenciando pipelines do Data Factory](data-factory-monitor-manage-pipelines.md) para obter detalhes. 

## <a name="specifying-parameters-for-a-hive-script"></a>Especificando os parâmetros para um script do Hive
Neste exemplo, os logs de jogo são incluídos diariamente no Armazenamento de Blobs do Azure e armazenados em uma pasta particionada com data e hora. Você deseja parametrizar o script de Hive e passar o local da pasta de entrada dinamicamente durante a execução, além de produzir a saída particionada com data e hora.

Para usar o script do Hive com parâmetros, faça o seguinte

* Defina os parâmetros em **defines**.

    ```JSON  
    {
        "name": "HiveActivitySamplePipeline",
          "properties": {
        "activities": [
             {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                      {
                        "name": "HiveSampleIn"
                      }
                ],
                "outputs": [
                      {
                        "name": "HiveSampleOut"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                      "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                      "scriptLinkedService": "StorageLinkedService",
                      "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                      },
                       "scheduler": {
                          "frequency": "Hour",
                          "interval": 1
                    }
                }
              }
        ]
      }
    }
    ```
* No script de Hive, consulte o parâmetro usando **${hiveconf:parameterName}** . 
  
    ```
    DROP TABLE IF EXISTS HiveSampleIn; 
    CREATE EXTERNAL TABLE HiveSampleIn 
    (
        ProfileID     string, 
        SessionStart     string, 
        Duration     int, 
        SrcIPAddress     string, 
        GameType     string
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 

    DROP TABLE IF EXISTS HiveSampleOut; 
    CREATE EXTERNAL TABLE HiveSampleOut 
    (
        ProfileID     string, 
        Duration     int
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

    INSERT OVERWRITE TABLE HiveSampleOut
    Select 
        ProfileID,
        SUM(Duration)
    FROM HiveSampleIn Group by ProfileID
    ```
  ## <a name="see-also"></a>Consulte também
* [Atividade Pig](data-factory-pig-activity.md)
* [Atividade MapReduce](data-factory-map-reduce.md)
* [Atividade de Transmissão do Hadoop](data-factory-hadoop-streaming-activity.md)
* [Invocar programas Spark](data-factory-spark.md)
* [Invocar scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

