---
title: Transformar dados usando a atividade de Hive do Hadoop
description: Saiba como usar a atividade de Hive em uma Azure Data Factory para executar consultas de Hive em um cluster sob demanda/próprio de HDInsight.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: seo-lt-2019
ms.date: 05/08/2019
ms.openlocfilehash: 7d312e4a00cdd2b62ee219df807f30c22f0c9790
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773939"
---
# <a name="transform-data-using-hadoop-hive-activity-in-azure-data-factory"></a>Transformar dados usando a atividade do Hive do Hadoop no Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-hive-activity.md)
> * [Versão atual](transform-data-using-hadoop-hive.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A atividade do Hive no HDInsight em um [pipeline](concepts-pipelines-activities.md) do Data Factory executa consultas do Hive em um cluster do HDInsight [de sua propriedade](compute-linked-services.md#azure-hdinsight-linked-service) ou [sob demanda](compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Este artigo se baseia no artigo sobre [atividades de transformação de dados](transform-data.md) que apresenta uma visão geral da transformação de dados e as atividades de transformação permitidas.

Se você é novo no Azure Data Factory, leia a [Introduction to Azure Data Factory](introduction.md) (Introdução ao Azure Data Factory) e siga o [Tutorial: transformar dados](tutorial-transform-data-spark-powershell.md) antes de ler este artigo. 

## <a name="syntax"></a>Sintaxe

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\HiveScripts\\MyHiveSript.hql",
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```
## <a name="syntax-details"></a>Detalhes da sintaxe
| Propriedade            | Descrição                                                  | Obrigatório |
| ------------------- | ------------------------------------------------------------ | -------- |
| name                | Nome da atividade                                         | Sim      |
| descrição         | Texto que descreve qual a utilidade da atividade                | Não       |
| type                | Para a atividade do Hive, o tipo de atividade é HDinsightHive        | Sim      |
| linkedServiceName   | Referência ao cluster do HDInsight registrado como um serviço vinculado no Data Factory. Para saber mais sobre esse serviço vinculado, consulte o artigo [Compute linked services](compute-linked-services.md) (Serviços de computação vinculados). | Sim      |
| scriptLinkedService | Referência a um serviço vinculado do Armazenamento do Azure usado para armazenar o script do Hive a ser executado. Somente os serviços vinculados do **[Armazenamento de Blobs do Azure](./connector-azure-blob-storage.md)** e do **[ADLS Gen2](./connector-azure-data-lake-storage.md)** são compatíveis aqui. Se você não especificar esse serviço vinculado, será usado o serviço vinculado do Armazenamento do Azure definido no serviço vinculado do HDInsight.  | Não       |
| scriptPath          | Forneça o caminho para o arquivo de script armazenado no Armazenamento do Azure referenciado por scriptLinkedService. O nome do arquivo diferencia maiúsculas de minúsculas. | Sim      |
| getDebugInfo        | Especifica quando os arquivos de log são copiados para o Armazenamento do Azure usado pelo cluster do HDInsight (ou) especificado por scriptLinkedService. Valores permitidos: Nenhum, Sempre ou Falha. Valor padrão: Nenhum. | Não       |
| argumentos           | Especifica uma matriz de argumentos para um trabalho do Hadoop. Os argumentos são passados como argumentos de linha de comando para cada tarefa. | Não       |
| defines             | Especifique parâmetros como pares chave-valor para referências no script do Hive. | Não       |
| queryTimeout        | Consultar o valor do tempo limite (em minutos). Aplicável quando o cluster do HDInsight está habilitado para o Enterprise Security Package. | Não       |

>[!NOTE]
>O valor padrão para queryTimeout é de 120 minutos. 

## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes artigos que explicam como transformar dados de outras maneiras: 

* [U-SQL activity](transform-data-using-data-lake-analytics.md) (Atividade do U-SQL)
* [Pig activity](transform-data-using-hadoop-pig.md) (Atividade do Pig)
* [MapReduce activity](transform-data-using-hadoop-map-reduce.md) (Atividade do MapReduce)
* [Hadoop Streaming activity](transform-data-using-hadoop-streaming.md) (Atividade de streaming do Hadoop)
* [Spark activity](transform-data-using-spark.md) (Atividade do Spark)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de execução de lote Azure Machine Learning Studio (clássica)](transform-data-using-machine-learning.md)
* [Stored procedure activity](transform-data-using-stored-procedure.md) (Atividade de procedimento armazenado)
