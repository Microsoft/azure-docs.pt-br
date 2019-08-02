---
title: Transformar dados usando a atividade do Spark no Azure Data Factory | Microsoft Docs
description: Saiba como transformar dados executando programas Spark em um pipeline do Azure Data Factory usando a atividade do Spark.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/31/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: c493dbc99edc794dd5a261dfc004c2c8c1cb6d52
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312081"
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Transformar dados usando a atividade do Spark no Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-spark.md)
> * [Versão atual](transform-data-using-spark.md)

A atividade do Spark em um [pipeline](concepts-pipelines-activities.md) do Data Factory executa um programa do Spark em [seu próprio](compute-linked-services.md#azure-hdinsight-linked-service) ou [sob demanda](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster HDInsight. Este artigo se baseia no artigo sobre [atividades de transformação de dados](transform-data.md) que apresenta uma visão geral da transformação de dados e as atividades de transformação permitidas. Quando você usa um serviço vinculado do Spark sob demanda, o Data Factory cria automaticamente um cluster Spark para você Just-In-Time a fim de processar os dados e, em seguida, exclui o cluster quando o processamento for concluído. 


## <a name="spark-activity-properties"></a>Propriedades da Atividade do Spark
Esta é a definição do JSON de exemplo de uma atividade do Spark:    

```json
{
    "name": "Spark Activity",
    "description": "Description",
    "type": "HDInsightSpark",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "sparkJobLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "rootPath": "adfspark",
        "entryFilePath": "test.py",
        "sparkConfig": {
            "ConfigItem1": "Value"
        },
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ]
    }
}
```

A tabela a seguir descreve as propriedades JSON usadas na definição de JSON:

| Propriedade              | DESCRIÇÃO                              | Obrigatório |
| --------------------- | ---------------------------------------- | -------- |
| name                  | Nome da atividade no pipeline.    | Sim      |
| description           | Texto que descreve o que a atividade faz.  | Não       |
| type                  | Para a atividade do Spark, o tipo de atividade é HDInsightSpark. | Sim      |
| linkedServiceName     | Nome do serviço vinculado do HDInsight Spark no qual o programa Spark é executado. Para saber mais sobre esse serviço vinculado, consulte o artigo [Compute linked services](compute-linked-services.md) (Serviços de computação vinculados). | Sim      |
| SparkJobLinkedService | O serviço vinculado ao Armazenamento do Azure que contém o arquivo de trabalho, dependências e os logs do Spark.  Se você não especificar um valor para essa propriedade, o armazenamento associado ao cluster HDInsight será usado. O valor desta propriedade só pode ser um serviço vinculado do Armazenamento do Microsoft Azure. | Não       |
| rootPath              | O contêiner de Blob do Azure e a pasta que contém o arquivo Spark. O nome do arquivo diferencia maiúsculas de minúsculas. Consulte a seção de estrutura de pasta (próxima seção) para obter detalhes sobre a estrutura desta pasta. | Sim      |
| entryFilePath         | Caminho relativo à pasta raiz do código/pacote Spark. O arquivo de entrada deve ser um arquivo. jar ou um arquivo Python. | Sim      |
| className             | Classe principal de Java/Spark do aplicativo      | Não       |
| arguments             | Uma lista de argumentos de linha de comando para o programa Spark. | Não       |
| proxyUser             | A conta de usuário a ser representada para execução do programa Spark | Não       |
| sparkConfig           | Especifique valores para as propriedades de configuração do Spark listadas no tópico: [Configuração do Spark – propriedades do aplicativo](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Não       |
| getDebugInfo          | Especifica quando os arquivos de log do Spark são copiados no armazenamento do Azure usado pelo cluster HDInsight (ou) especificado por sparkJobLinkedService. Valores permitidos: Nenhum, Sempre ou Falha. Valor padrão: Nenhuma. | Não       |

## <a name="folder-structure"></a>Estrutura de pastas
Os trabalhos do Spark são mais extensíveis do que os trabalhos do Pig/Hive. Para trabalhos do Spark, você pode fornecer várias dependências, como pacotes jar (colocados no CLASSPATH de java), arquivos do python (colocados no PYTHONPATH) e outros arquivos.

Crie a seguinte estrutura de pastas no armazenamento de Blobs do Azure referenciado pelo serviço vinculado ao HDInsight. Depois, carregue os arquivos dependentes nas subpastas apropriadas na pasta raiz, representada por **entryFilePath**. Por exemplo, carregue arquivos do python na subpasta pyFiles e os arquivos jar na subpasta jars da pasta raiz. Em tempo de execução, o serviço Data Factory espera a seguinte estrutura de pastas no Armazenamento de Blobs do Azure:     

| Path                  | DESCRIÇÃO                              | Obrigatório | Type   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.` (raiz)            | O caminho raiz do trabalho do Spark no serviço vinculado ao armazenamento | Sim      | Pasta |
| &lt;definido pelo usuário&gt; | O caminho que aponta para o arquivo de entrada do trabalho do Spark | Sim      | Arquivo   |
| ./jars                | Todos os arquivos nessa pasta são carregados e colocados no classpath de java do cluster | Não       | Pasta |
| ./pyFiles             | Todos os arquivos nessa pasta são carregados e colocados no PYTHONPATH do cluster | Não       | Pasta |
| ./files               | Todos os arquivos nessa pasta são carregados e colocados no diretório de trabalho executor | Não       | Pasta |
| ./archives            | Todos os arquivos nessa pasta são descompactados | Não       | Pasta |
| ./logs                | A pasta que contém os logs do cluster do Spark. | Não       | Pasta |

Veja um exemplo de um armazenamento que contém dois arquivos de trabalho do Spark no Armazenamento de Blobs do Azure referenciado pelo serviço vinculado ao HDInsight.

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes artigos que explicam como transformar dados de outras maneiras: 

* [U-SQL activity](transform-data-using-data-lake-analytics.md) (Atividade do U-SQL)
* [Hive activity](transform-data-using-hadoop-hive.md) (Atividade do Hive)
* [Pig activity](transform-data-using-hadoop-pig.md) (Atividade do Pig)
* [MapReduce activity](transform-data-using-hadoop-map-reduce.md) (Atividade do MapReduce)
* [Hadoop Streaming activity](transform-data-using-hadoop-streaming.md) (Atividade de streaming do Hadoop)
* [Spark activity](transform-data-using-spark.md) (Atividade do Spark)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Batch Execution activity](transform-data-using-machine-learning.md) (Atividade de execução em lotes do Machine Learning)
* [Stored procedure activity](transform-data-using-stored-procedure.md) (Atividade de procedimento armazenado)
