---
title: Transformar dados usando a atividade do MapReduce do Hadoop no Azure Data Factory | Microsoft Docs
description: Saiba como processar dados executando programas MapReduce do Hadoop em um cluster do Azure HDInsight em um Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: ccc194dd4120762a30da3ad28cdabed6faf53ba2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611512"
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Transformar dados usando a atividade do MapReduce do Hadoop no Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-map-reduce.md)
> * [Versão atual](transform-data-using-hadoop-map-reduce.md)

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

| Propriedade          | DESCRIÇÃO                              | Obrigatório |
| ----------------- | ---------------------------------------- | -------- |
| nome              | Nome da atividade                     | Sim      |
| descrição       | Texto que descreve qual a utilidade da atividade | Não       |
| Tipo              | Para a atividade do MapReduce, o tipo de atividade é HDinsightMapReduce | Sim      |
| linkedServiceName | Referência ao cluster do HDInsight registrado como um serviço vinculado no Data Factory. Para saber mais sobre esse serviço vinculado, consulte o artigo [Compute linked services](compute-linked-services.md) (Serviços de computação vinculados). | Sim      |
| className         | Nome da classe a ser executada         | Sim      |
| jarLinkedService  | Referência a um serviço vinculado do Armazenamento do Azure usado para armazenar os arquivos Jar. Se você não especificar esse serviço vinculado, será usado o serviço vinculado do Armazenamento do Azure definido no serviço vinculado do HDInsight. | Não        |
| jarFilePath       | Forneça o caminho para os arquivos Jar armazenados no Armazenamento do Azure referenciado por jarLinkedService. O nome do arquivo diferencia maiúsculas de minúsculas. | Sim      |
| jarlibs           | Matriz de cadeia de caracteres do caminho para os arquivos de biblioteca Jar referenciados pelo trabalho armazenado no Armazenamento do Azure referenciado por jarLinkedService. O nome do arquivo diferencia maiúsculas de minúsculas. | Não        |
| getDebugInfo      | Especifica quando os arquivos de log são copiados para o Armazenamento do Azure usado pelo cluster do HDInsight (ou) especificado por jarLinkedService. Valores permitidos: Nenhum, Sempre ou Falha. Valor padrão: Nenhuma. | Não        |
| argumentos         | Especifica uma matriz de argumentos para um trabalho do Hadoop. Os argumentos são passados como argumentos de linha de comando para cada tarefa. | Não        |
| define           | Especifique parâmetros como pares chave-valor para referências no script do Hive. | Não        |



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
Você pode especificar argumentos para o programa MapReduce na seção **argumentos**. Em tempo de execução, você verá alguns argumentos extras (por exemplo: mapreduce.job.tags) da estrutura MapReduce. Para diferenciar seus argumentos com os argumentos MapReduce, considere usar opção e valor como argumentos, conforme mostrado no exemplo a seguir (- s, --input - output etc... são opções seguidas imediatamente por seus valores).

## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes artigos que explicam como transformar dados de outras maneiras:

* [U-SQL activity](transform-data-using-data-lake-analytics.md) (Atividade do U-SQL)
* [Hive activity](transform-data-using-hadoop-hive.md) (Atividade do Hive)
* [Pig activity](transform-data-using-hadoop-pig.md) (Atividade do Pig)
* [Hadoop Streaming activity](transform-data-using-hadoop-streaming.md) (Atividade de streaming do Hadoop)
* [Spark activity](transform-data-using-spark.md) (Atividade do Spark)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Batch Execution activity](transform-data-using-machine-learning.md) (Atividade de execução em lotes do Machine Learning)
* [Stored procedure activity](transform-data-using-stored-procedure.md) (Atividade de procedimento armazenado)
