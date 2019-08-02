---
title: Pipelines e atividades no Azure Data Factory | Microsoft Docs
description: Aprenda sobre pipelines e atividades no Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: shlo
ms.openlocfilehash: 63a86fb9498c7c1b1cd527accca84c83a28e01c3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65788666"
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Pipelines e atividades no Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-create-pipelines.md)
> * [Versão atual](concepts-pipelines-activities.md)

Este artigo o ajuda a compreender pipelines e atividades no Azure Data Factory e a usá-los para construir fluxos de trabalho orientados a dados de ponta a ponta para seus cenários de movimentação e processamento de dados.

## <a name="overview"></a>Visão geral
Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline é um agrupamento lógico de atividades que juntas executam uma tarefa. Por exemplo, um pipeline pode conter um conjunto de atividades que ingerem e limpam dados de log e, em seguida, iniciam um trabalho do Spark em um cluster HDInsight para analisar os dados de log. A vantagem disso é que o pipeline permite que você gerencie atividades como um conjunto, em vez de cada uma individualmente. Por exemplo, você pode implantar e agendar o pipeline, em vez de atividades de forma independente.

As atividades em um pipeline definem ações para executar em seus dados. Por exemplo, você pode usar uma atividade de cópia para copiar dados de um SQL Server local para um Armazenamento de Blobs do Azure. Em seguida, usar uma atividade Hive que executa um script Hive em um cluster HDInsight do Azure a fim de processar/transformar dados do armazenamento de blobs para gerar dados de saída. Por fim, usar uma segunda atividade de cópia para copiar os dados de saída em um SQL Data Warehouse do Azure sobre o qual as soluções de relatório de BI (business intelligence) são criadas.

O Data Factory dá suporte a três tipos de atividades: [atividades de movimentação de dados](copy-activity-overview.md), [atividades de transformação de dados](transform-data.md) e [atividades de controle](control-flow-web-activity.md). Uma atividade pode não usar ou usar vários [conjuntos de dados](concepts-datasets-linked-services.md) de entrada e gerar um ou mais [conjuntos de dados](concepts-datasets-linked-services.md) de saída. O seguinte diagrama mostra a relação entre pipeline, atividade e conjunto de dados no Data Factory:

![Relação entre pipeline, atividade e conjunto de dados](media/concepts-pipelines-activities/relationship-between-dataset-pipeline-activity.png)

Um conjunto de dados de entrada representa a entrada de uma atividade no pipeline e um conjunto de dados de saída representa a saída da atividade. Conjuntos de dados identificam dados em armazenamentos de dados diferentes, como tabelas, arquivos, pastas e documentos. Depois de criar um conjunto de dados, você pode usá-lo com atividades no pipeline. Por exemplo, um conjunto de dados pode ser o conjunto de dados de entrada/saída de uma atividade de Cópia ou uma atividade do HDInsightHive. Para saber mais sobre conjuntos de dados, confira o artigo [Conjuntos de dados no Azure Data Factory](concepts-datasets-linked-services.md).

## <a name="data-movement-activities"></a>Atividades de movimentação de dados
A Atividade de Cópia no Data Factory copia os dados de um repositório de dados de origem para um repositório de dados de coletor. O Data Factory dá suporte aos armazenamentos de dados listados na tabela nesta seção. Os dados de qualquer origem podem ser gravados em qualquer coletor. Clique em um repositório de dados para saber como copiar dados dentro e fora do repositório.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

Para obter mais informações, consulte [Atividade de Cópia – Visão Geral](copy-activity-overview.md).

## <a name="data-transformation-activities"></a>Atividades de transformação de dados
O Azure Data Factory dá suporte às seguintes atividades de transformação, que podem ser adicionadas aos pipelines individualmente ou encadeadas a outra atividade.

Atividades de transformação de dados | Ambiente de computação
---------------------------- | -------------------
[Hive](transform-data-using-hadoop-hive.md) | HDInsight [Hadoop]
[Pig](transform-data-using-hadoop-pig.md) | HDInsight [Hadoop]
[MapReduce](transform-data-using-hadoop-map-reduce.md) | HDInsight [Hadoop]
[Streaming do Hadoop](transform-data-using-hadoop-streaming.md) | HDInsight [Hadoop]
[Spark](transform-data-using-spark.md) | HDInsight [Hadoop]
[Atividades de Machine Learning: Execução de lote e Recurso de atualização](transform-data-using-machine-learning.md) | Azure VM
[Procedimento armazenado](transform-data-using-stored-procedure.md) | SQL Azure, Azure SQL Data Warehouse ou SQL Server
[U-SQL](transform-data-using-data-lake-analytics.md) | Análise Azure Data Lake
[Código Personalizado](transform-data-using-dotnet-custom-activity.md) | Lote do Azure
[Databricks Notebook](transform-data-databricks-notebook.md) | Azure Databricks

Para obter mais informações, confira o artigo [Atividades de transformação de dados](transform-data.md).

## <a name="control-activities"></a>Atividades de controle
Há suporte para as seguintes atividades de fluxo de controle:

Atividade de controle | DESCRIÇÃO
---------------- | -----------
[Atividade de execução de pipeline](control-flow-execute-pipeline-activity.md) | A atividade de execução de pipeline permite que um pipeline do Data Factory invoque outro pipeline.
[ForEachActivity](control-flow-for-each-activity.md) | A atividade ForEach define um fluxo de controle repetitivo no seu pipeline. Essa atividade é usada para iterar em uma coleção e executa atividades especificadas em um loop. A implementação dessa atividade em loop é semelhante à estrutura em loop Foreach nas linguagens de programação.
[WebActivity](control-flow-web-activity.md) | A atividade da Web pode ser usada para chamar um ponto de extremidade REST personalizado de um pipeline do Data Factory. Você pode passar conjuntos de dados e serviços vinculados a serem consumidos e acessados pela atividade.
[Atividade de pesquisa](control-flow-lookup-activity.md) | A atividade de pesquisa pode ser usada para ler ou procurar um registro/nome de tabela/valor de qualquer fonte externa. Essa saída pode referenciada pelas atividades com êxito.
[Atividade de obtenção de metadados](control-flow-get-metadata-activity.md) | A atividade GetMetadata pode ser usada para recuperar metadados de todos os dados no Azure Data Factory.
[Atividade Until](control-flow-until-activity.md) | Implementa o loop Do-Until, que é semelhante à estrutura de looping Do-Until em linguagens de programação. Ela executa um conjunto de atividades em um loop até que a condição associada à atividade seja avaliada como verdadeira. Especifique um valor de tempo limite para a atividade Until no Data Factory.
[Atividade de Condição Se](control-flow-if-condition-activity.md) | A Condição If pode ser usada para ramificar com base em condições que são avaliadas como true ou false. A atividade If Condition fornece a mesma funcionalidade que uma instrução if fornece em linguagens de programação. Ela avalia um conjunto de atividades quando a condição é avaliada como `true` e outro conjunto de atividades quando a condição é avaliada como `false`.
[Atividade de espera](control-flow-wait-activity.md) | Quando você usa uma atividade de espera em um pipeline, o pipeline aguarda o período de tempo especificado antes de continuar com a execução de atividades subsequentes.

## <a name="pipeline-json"></a>Pipeline de JSON
Veja como um pipeline é definido no formato JSON:

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description": "pipeline description",
        "activities":
        [
        ],
        "parameters": {
         }
    }
}
```

Marca | DESCRIÇÃO | Type | Obrigatório
--- | ----------- | ---- | --------
name | Nome do pipeline. Especifique um nome que represente a ação executada pelo pipeline. <br/><ul><li>Número máximo de caracteres: 140</li><li>Deve começar com uma letra, um número ou um sublinhado (\_)</li><li>Os seguintes caracteres não são permitidos: “.”, “+”, “?”, “/”, “<”,”>”,”*”,”%”,”&”,”:”,”\”</li></ul> | String | Sim
description | Especifique o texto descrevendo para que o pipeline é usado. | String | Não
activities | A seção **Atividades** pode ter uma ou mais atividades definidas dentro dela. Confira a seção [Atividade JSON](#activity-json) para obter detalhes sobre o elemento das atividades JSON. | Matriz | Sim
parameters | A seção **parâmetros** pode ter um ou mais parâmetros definidos no pipeline, tornando seu pipeline flexível para reutilização. | List | Não

## <a name="activity-json"></a>Atividade JSON
A seção **Atividades** pode ter uma ou mais atividades definidas dentro dela. Há dois principais tipos de atividades: atividades de execução e controle.

### <a name="execution-activities"></a>Atividades de execução
As atividades de execução incluem [atividades de movimentação de dados](#data-movement-activities) e de [transformação de dados](#data-transformation-activities). Elas têm a seguinte estrutura de nível superior:

```json
{
    "name": "Execution Activity Name",
    "description": "description",
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "linkedServiceName": "MyLinkedService",
    "policy":
    {
    },
    "dependsOn":
    {
    }
}
```

A seguinte tabela descreve as propriedades na definição de JSON da atividade:

Marca | DESCRIÇÃO | Obrigatório
--- | ----------- | ---------
name | Nome da atividade. Especifique um nome que represente a ação executada pela atividade. <br/><ul><li>Número máximo de caracteres: 55</li><li>Deve começar com uma letra, um número ou um sublinhado (\_)</li><li>Os seguintes caracteres não são permitidos: “.”, “+”, “?”, “/”, “<”,”>”,”*”,”%”,”&”,”:”,”\” | Sim</li></ul>
description | Texto que descreve para que a atividade é usada | Sim
type | Tipo da atividade. Confira as seções [Atividades de movimentação de dados](#data-movement-activities), [Atividades de transformação de dados](#data-transformation-activities) e [Atividades de controle](#control-activities) para diferentes tipos de atividade. | Sim
linkedServiceName | Nome do serviço vinculado usado pela atividade.<br/><br/>Uma atividade pode exigir que você especifique o serviço vinculado que é vinculado ao ambiente de computação necessário. | Sim para Atividade de HDInsight, Atividade de Pontuação de Lote do Azure Machine Learning e Atividade de Procedimento Armazenado. <br/><br/>Não para todas as outros
typeProperties | As propriedades na seção typeProperties dependem de cada tipo de atividade. Para ver as propriedades de tipo para uma atividade, clique em links para a atividade na seção anterior. | Não
policy | Políticas que afetam o comportamento de tempo de execução da atividade. Essa propriedade inclui o comportamento de tempo limite e de repetição. Se não forem especificados, os valores padrão serão usados. Para obter mais informações, consulte a seção [Política de atividade](#activity-policy). | Não
dependsOn | Esta propriedade é usada para definir as dependências de atividade e o modo como as atividades subsequentes dependem de atividades anteriores. Para obter mais informações, consulte a seção [Dependência de atividade](#activity-dependency) | Não

### <a name="activity-policy"></a>Política de atividade
Políticas afetam o comportamento de tempo de execução de uma atividade, oferecendo opções de configurabilidade. Políticas de atividade só estão disponíveis para atividades de execução.

### <a name="activity-policy-json-definition"></a>Definição JSON de política de atividade

```json
{
    "name": "MyPipelineName",
    "properties": {
      "activities": [
        {
          "name": "MyCopyBlobtoSqlActivity"
          "type": "Copy",
          "typeProperties": {
            ...
          },
         "policy": {
            "timeout": "00:10:00",
            "retry": 1,
            "retryIntervalInSeconds": 60,
            "secureOutput": true
         }
        }
      ],
        "parameters": {
           ...
        }
    }
}
```

Nome JSON | DESCRIÇÃO | Valores Permitidos | Obrigatório
--------- | ----------- | -------------- | --------
timeout | Especifica o tempo limite para a atividade ser executada. | Timespan | Não. O tempo limite padrão é 7 dias.
retry | Número máximo de novas tentativas | Número inteiro | Não. O padrão é 0
retryIntervalInSeconds | O intervalo entre tentativas de repetição em segundos | Número inteiro | Não. O padrão é de 30 segundos
secureOutput | Quando definido como true, a saída da atividade é considerada segura e não será registrada em log para monitoramento. | Boolean | Não. O padrão é falso.

### <a name="control-activity"></a>Atividade de controle
As atividades de controle têm a seguinte estrutura de nível superior:

```json
{
    "name": "Control Activity Name",
    "description": "description",
    "type": "<ActivityType>",
    "typeProperties":
    {
    },
    "dependsOn":
    {
    }
}
```

Marca | DESCRIÇÃO | Obrigatório
--- | ----------- | --------
name | Nome da atividade. Especifique um nome que represente a ação executada pela atividade.<br/><ul><li>Número máximo de caracteres: 55</li><li>Deve começar com uma letra, um número ou um sublinhado (\_)</li><li>Os seguintes caracteres não são permitidos: “.”, “+”, “?”, “/”, “<”,”>”,”*”,”%”,”&”,”:”,”\” | Sim</li><ul>
description | Texto que descreve para que a atividade é usada | Sim
type | Tipo da atividade. Confira as seções [atividades de movimentação de dados](#data-movement-activities), [atividades de transformação de dados](#data-transformation-activities) e [atividades de controle](#control-activities) para diferentes tipos de atividade. | Sim
typeProperties | As propriedades na seção typeProperties dependem de cada tipo de atividade. Para ver as propriedades de tipo para uma atividade, clique em links para a atividade na seção anterior. | Não
dependsOn | Essa propriedade é usada para definir a dependência de atividade e o modo como as atividades subsequentes dependem de atividades anteriores. Para obter mais informações, consulte [dependência de atividade](#activity-dependency). | Não

### <a name="activity-dependency"></a>Dependência de atividade
A dependência de atividade define o modo como atividades subsequentes dependem de atividades anteriores, determinando a condição para que a execução continue ou não para a próxima tarefa. Uma atividade pode depender de uma ou várias atividades anteriores com condições de dependência diferentes.

As condições de dependência diferentes são: Bem-sucedido, Com falha, Ignorado, Concluído.

Por exemplo, se um pipeline tem atividade A -> atividade B, os diferentes cenários que podem acontecer são:

- A atividade B tem a condição de dependência sobre a atividade A com **bem-sucedido**: A atividade B só será executada se a atividade A tiver um status final de bem-sucedido
- A atividade B tem a condição de dependência sobre a atividade A com **falha**: A atividade B só será executada se a atividade A tiver um status final de falha
- A atividade B tem a condição de dependência sobre a atividade A com **concluído**: A atividade B será executada se a atividade A tiver um status final de bem-sucedido ou falha
- A atividade B tem a condição de dependência sobre a atividade A com **ignorado**: A atividade B será executada se a atividade A tiver um status final de ignorado. Ignorada ocorre no cenário de Atividade X -> Atividade Y -> Atividade Z, em que cada atividade é executada somente se a atividade anterior tem êxito. Se a Atividade X falhar, a Atividade Y terá um status de "Ignorada", porque nunca será executada. Da mesma forma, a Atividade Z também terá um status de "Ignorada".

#### <a name="example-activity-2-depends-on-the-activity-1-succeeding"></a>Exemplo: A Atividade 2 depende do êxito da Atividade 1

```json
{
    "name": "PipelineName",
    "properties":
    {
        "description": "pipeline description",
        "activities": [
         {
            "name": "MyFirstActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            }
        },
        {
            "name": "MySecondActivity",
            "type": "Copy",
            "typeProperties": {
            },
            "linkedServiceName": {
            },
            "dependsOn": [
            {
                "activity": "MyFirstActivity",
                "dependencyConditions": [
                    "Succeeded"
                ]
            }
          ]
        }
      ],
      "parameters": {
       }
    }
}

```

## <a name="sample-copy-pipeline"></a>Pipeline de cópia de exemplo
No pipeline de exemplo a seguir, há uma atividade do tipo **Cópia** in the **atividades** . Neste exemplo, a [atividade de cópia](copy-activity-overview.md) copia dados de um Armazenamento de Blobs do Azure para um banco de dados SQL do Azure.

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "policy": {
          "retry": 2,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
Observe os seguintes pontos:

- Na seção de atividades, há apenas uma atividade cujo **tipo** é definido como **Copy**.
- A entrada da atividade é definida como **InputDataset** e a saída da atividade é definida como **OutputDataset**. Confira o artigo [Conjuntos de dados](concepts-datasets-linked-services.md) para definir conjuntos de dados em JSON.
- Na seção **typeProperties**, **BlobSource** é especificado como o tipo de origem e **SqlSink** é especificado como o tipo de coletor. Na seção [atividades de movimentação de dados](#data-movement-activities), clique no armazenamento de dados que você quer usar como uma fonte ou um coletor para aprender mais sobre como mover dados bidirecionalmente nesse armazenamento de dados.

Para um passo a passo completo sobre como criar esse pipeline, consulte [Guia de início rápido: criar um data factory](quickstart-create-data-factory-powershell.md).

## <a name="sample-transformation-pipeline"></a>Pipeline de transformação de exemplo
No pipeline de exemplo a seguir, há uma atividade do tipo **HDInsightHive** in the **atividades** . Neste exemplo, a [atividade de Hive do HDInsight](transform-data-using-hadoop-hive.md) transforma os dados de um Armazenamento de Blobs do Azure executando um arquivo de script do Hive em um cluster Hadoop do HDInsight do Azure.

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ]
    }
}
```
Observe os seguintes pontos:

- Na seção de atividades, há apenas uma atividade cujo **tipo** é definido como **HDInsightHive**.
- O arquivo de script do Hive, **partitionweblogs.hql**, é armazenado na conta de armazenamento do Azure (especificada pelo scriptLinkedService chamado AzureStorageLinkedService) e na pasta script no contêiner `adfgetstarted`.
- A seção `defines` é usada para especificar as configurações de tempo de execução passadas para o script do hive como valores de configuração de Hive (por exemplo, $`{hiveconf:inputtable}`, `${hiveconf:partitionedtable}`).

A seção **typeProperties** é diferente para cada atividade de transformação. Para saber mais sobre as propriedades de tipo com suporte para uma atividade de transformação, clique na atividade de transformação nas [Atividades de transformação de dados](#data-transformation-activities).

Para obter uma explicação completa da criação desse pipeline, confira [Tutorial: transformar dados usando o Spark](tutorial-transform-data-spark-powershell.md).

## <a name="multiple-activities-in-a-pipeline"></a>Várias atividades em um pipeline
Os dois pipelines de exemplo anteriores têm apenas uma atividade neles. Você pode ter mais de uma atividade em um pipeline. Se você tiver várias atividades em um pipeline e as atividades subsequentes não forem dependentes das atividades anteriores, as atividades poderão ser executadas em paralelo.

Você pode encadear duas atividades usando a [dependência de atividade](#activity-dependency), que define o modo como atividades subsequentes dependem de atividades anteriores, determinando a condição para que a execução continue ou não para a próxima tarefa. Uma atividade pode depender de uma ou mais atividades anteriores com condições de dependência diferentes.

## <a name="scheduling-pipelines"></a>Agendando pipelines
Pipelines são agendados por gatilhos. Há diferentes tipos de gatilhos (gatilho do Agendador, que permite que pipelines sejam disparados em um agendamento de relógio, bem como gatilho manual, que dispara os pipelines sob demanda). Para obter mais informações sobre gatilhos, consulte o artigo [gatilhos e execução de pipeline](concepts-pipeline-execution-triggers.md).

Para que o gatilho dispare uma execução de pipeline, você deve incluir uma referência de pipeline do pipeline específico na definição do gatilho. Os pipelines e os gatilhos têm uma relação n-m. Vários gatilhos podem disparar um único pipeline e o mesmo gatilho pode disparar vários pipelines. Uma vez definido o gatilho, você deve iniciar o gatilho para que ele comece a disparar o pipeline. Para obter mais informações sobre gatilhos, consulte o artigo [gatilhos e execução de pipeline](concepts-pipeline-execution-triggers.md).

Por exemplo, digamos que você tenha um gatilho de agendador, "Gatilho A", que você gostaria que disparasse a execução do seu pipeline, "MyCopyPipeline". Você define o gatilho conforme mostrado no exemplo a seguir:

### <a name="trigger-a-definition"></a>Definição do gatilho A

```json
{
  "name": "TriggerA",
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      ...
      }
    },
    "pipeline": {
      "pipelineReference": {
        "type": "PipelineReference",
        "referenceName": "MyCopyPipeline"
      },
      "parameters": {
        "copySourceName": "FileSource"
      }
    }
  }
}
```



## <a name="next-steps"></a>Próximas etapas
Confira os tutoriais a seguir para obter instruções passo a passo para criar pipelines com atividades:

- [Criar um pipeline com uma atividade de cópia](quickstart-create-data-factory-powershell.md)
- [Crie um pipeline com uma atividade de transformação de dados](tutorial-transform-data-spark-powershell.md)
