---
title: Transformar dados usando a atividade MapReduce do Hadoop
description: Saiba como processar dados executando programas MapReduce do Hadoop em um cluster do Azure HDInsight em um Azure Data Factory.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: seo-lt-2019
ms.date: 05/08/2020
ms.openlocfilehash: f03906586d6226c92cfa69e1a139d4c876cbf723
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100375877"
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Transformar dados usando a atividade do MapReduce do Hadoop no Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-map-reduce.md)
> * [Versão atual](transform-data-using-hadoop-map-reduce.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A atividade do MapReduce no HDInsight em um [pipeline](concepts-pipelines-activities.md) do Data Factory invoca programa MapReduce em um cluster do HDInsight [de sua propriedade](compute-linked-services.md#azure-hdinsight-linked-service) ou [sob demanda](compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Este artigo se baseia no artigo sobre [atividades de transformação de dados](transform-data.md) que apresenta uma visão geral da transformação de dados e as atividades de transformação permitidas.

Se você é novo no Azure Data Factory, leia a [Introdução ao Azure Data Factory](introduction.md) e siga o tutorial: [Transformar dados](tutorial-transform-data-spark-powershell.md) antes de ler este artigo.

Consulte [Pig](transform-data-using-hadoop-pig.md) e [Hive](transform-data-using-hadoop-hive.md) para obter detalhes sobre a execução de scripts do Pig/Hive em um cluster do HDInsight de um pipeline usando atividades do Pig e do Hive no HDInsight.

## <a name="syntax"></a>Sintaxe

```json
{
    "name": "Map Reduce Activity",
    "description": "Description",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.myorg.SampleClass",
        "jarLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "MyAzureStorage/jars/sample.jar",
        "getDebugInfo": "Failure",
        "arguments": [
            "-SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```

## <a name="syntax-details"></a>Detalhes da sintaxe

| Propriedade          | Descrição                              | Obrigatório |
| ----------------- | ---------------------------------------- | -------- |
| name              | Nome da atividade                     | Sim      |
| descrição       | Texto que descreve qual a utilidade da atividade | Não       |
| type              | Para a atividade do MapReduce, o tipo de atividade é HDinsightMapReduce | Sim      |
| linkedServiceName | Referência ao cluster do HDInsight registrado como um serviço vinculado no Data Factory. Para saber mais sobre esse serviço vinculado, consulte o artigo [Compute linked services](compute-linked-services.md) (Serviços de computação vinculados). | Sim      |
| className         | Nome da classe a ser executada         | Sim      |
| jarLinkedService  | Referência a um serviço vinculado do Armazenamento do Azure usado para armazenar os arquivos Jar. Somente os serviços vinculados do **[Armazenamento de Blobs do Azure](./connector-azure-blob-storage.md)** e do **[ADLS Gen2](./connector-azure-data-lake-storage.md)** são compatíveis aqui. Se você não especificar esse serviço vinculado, será usado o serviço vinculado do Armazenamento do Azure definido no serviço vinculado do HDInsight. | Não       |
| jarFilePath       | Forneça o caminho para os arquivos Jar armazenados no Armazenamento do Azure referenciado por jarLinkedService. O nome do arquivo diferencia maiúsculas de minúsculas. | Sim      |
| jarlibs           | Matriz de cadeia de caracteres do caminho para os arquivos de biblioteca Jar referenciados pelo trabalho armazenado no Armazenamento do Azure referenciado por jarLinkedService. O nome do arquivo diferencia maiúsculas de minúsculas. | Não       |
| getDebugInfo      | Especifica quando os arquivos de log são copiados para o Armazenamento do Azure usado pelo cluster do HDInsight (ou) especificado por jarLinkedService. Valores permitidos: Nenhum, Sempre ou Falha. Valor padrão: Nenhum. | Não       |
| argumentos         | Especifica uma matriz de argumentos para um trabalho do Hadoop. Os argumentos são passados como argumentos de linha de comando para cada tarefa. | Não       |
| defines           | Especifique parâmetros como pares chave-valor para referências no script do Hive. | Não       |



## <a name="example"></a>Exemplo
Você pode usar a atividade do HDInsight MapReduce para executar qualquer arquivo de jar do MapReduce em um cluster do HDInsight. Na seguinte definição de JSON de exemplo de uma pipeline, a Atividade HDInsight é configurada para executar um arquivo JAR do Mahout.

```json
{
    "name": "MapReduce Activity for Mahout",
    "description": "Custom MapReduce to generate Mahout result",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
        "jarLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
        "arguments": [
            "-s",
            "SIMILARITY_LOGLIKELIHOOD",
            "--input",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
            "--output",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
            "--maxSimilaritiesPerItem",
            "500",
            "--tempDir",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
        ]
    }
}
```
Você pode especificar argumentos para o programa MapReduce na seção **argumentos**. Em runtime, você verá alguns argumentos extras (por exemplo: mapreduce.job.tags) da estrutura MapReduce. Para diferenciar seus argumentos com os argumentos MapReduce, considere usar opção e valor como argumentos, conforme mostrado no exemplo a seguir (-s,--input,--output etc. são opções seguidas imediatamente pelos respectivos valores).

## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes artigos que explicam como transformar dados de outras maneiras:

* [U-SQL activity](transform-data-using-data-lake-analytics.md) (Atividade do U-SQL)
* [Hive activity](transform-data-using-hadoop-hive.md) (Atividade do Hive)
* [Pig activity](transform-data-using-hadoop-pig.md) (Atividade do Pig)
* [Hadoop Streaming activity](transform-data-using-hadoop-streaming.md) (Atividade de streaming do Hadoop)
* [Spark activity](transform-data-using-spark.md) (Atividade do Spark)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de execução de lote Azure Machine Learning Studio (clássica)](transform-data-using-machine-learning.md)
* [Stored procedure activity](transform-data-using-stored-procedure.md) (Atividade de procedimento armazenado)