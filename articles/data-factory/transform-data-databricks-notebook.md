---
title: Transformar dados com o databricks Notebook
description: Saiba como processar ou transformar dados executando um notebook do databricks no Azure Data Factory.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: fea572c2e75f62b5e7e7b4634e37da348bdcdaf1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102183481"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Transformar dados executando um notebook do Databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A atividade de Azure Databricks notebook em um [pipeline de data Factory](concepts-pipelines-activities.md) executa um notebook do databricks em seu espaço de trabalho do Azure Databricks. Este artigo se baseia no artigo sobre [atividades de transformação de dados](transform-data.md) que apresenta uma visão geral da transformação de dados e as atividades de transformação permitidas. O Azure Databricks é uma plataforma gerenciada para executar o Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Definição de atividade do Notebook Databricks

A seguir está a definição JSON de exemplo de uma Atividade de Notebook do Databricks:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            },
            "libraries": [
                {
                "jar": "dbfs:/docs/library.jar"
                }
            ]
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Propriedades da atividade de Notebook do Databricks

A tabela a seguir descreve as propriedades JSON usadas na definição de JSON:

|Propriedade|Descrição|Obrigatório|
|---|---|---|
|name|Nome da atividade no pipeline.|Sim|
|descrição|Texto que descreve o que a atividade faz.|Não|
|type|Para Atividade de Notebook do Databricks, o tipo da atividade é DatabricksNotebook.|Sim|
|linkedServiceName|Nome do serviço vinculado do Databricks no qual o notebook do Databricks executa. Para saber mais sobre esse serviço vinculado, consulte o artigo [Compute linked services](compute-linked-services.md) (Serviços de computação vinculados).|Sim|
|notebookPath|O caminho absoluto do notebook a ser executado no workspace do Databricks. Esse caminho deve começar com uma barra.|Sim|
|baseParameters|Uma matriz de pares chave-valor. Parâmetros básicos podem ser utilizados para cada execução de atividade. Se o notebook utilizar um parâmetro que não for especificado, será usado o valor padrão do notebook. Encontre mais informações sobre os parâmetros em [Notebooks do Databricks](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Não|
|bibliotecas|Uma lista de bibliotecas a serem instaladas no cluster, que executará o trabalho. Pode ser uma matriz de \<string, object> .|Não|

## <a name="supported-libraries-for-databricks-activities"></a>Bibliotecas com suporte para atividades do Databricks

Na definição da atividade do databricks acima, você especifica esses tipos de biblioteca: *jar*, *ovo*, *WHL*, *Maven*, *PyPI*, *Cran*.

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
        {
            "whl": "dbfs:/mnt/libraries/mlflow-0.0.1.dev0-py2-none-any.whl"
        },
        {
            "whl": "dbfs:/mnt/libraries/wheel-libraries.wheelhouse.zip"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

Para obter mais detalhes, consulte a [documentação do Databricks](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary) para tipos de biblioteca.

## <a name="passing-parameters-between-notebooks-and-data-factory"></a>Passando parâmetros entre notebooks e o Data Factory

Você pode passar data factory parâmetros para blocos de anotações usando a propriedade *baseparameters* na atividade do databricks.

Em determinados casos, talvez seja necessário repassar determinados valores do bloco de anotações para data factory, que pode ser usado para o fluxo de controle (verificações condicionais) no data factory ou ser consumido pelas atividades downstream (o limite de tamanho é 2MB).

1. No bloco de anotações, você pode chamar [dbutils. notebook. Exit ("ReturnValue")](/azure/databricks/notebooks/notebook-workflows#notebook-workflows-exit) e o "ReturnValue" correspondente será retornado para data Factory.

2. Você pode consumir a saída em data factory usando expressão como `@{activity('databricks notebook activity name').output.runOutput}` . 

   > [!IMPORTANT]
   > Se você estiver passando um objeto JSON, poderá recuperar valores acrescentando nomes de propriedade. Exemplo: `@{activity('databricks notebook activity name').output.runOutput.PropertyName}`

## <a name="how-to-upload-a-library-in-databricks"></a>Como carregar uma biblioteca no Databricks

### <a name="you-can-use-the-workspace-ui"></a>Você pode usar a interface do usuário do espaço de trabalho:

1. [Usar a interface do usuário do databricks Workspace](/azure/databricks/libraries/#create-a-library)

2. Para obter o caminho dBFS da biblioteca adicionada usando a interface do usuário, você pode usar a [CLI do databricks](/azure/databricks/dev-tools/cli/#install-the-cli).

   Normalmente, as bibliotecas Jar são armazenadas em dbfs:/FileStore/jars ao usar a interface do usuário. Você pode listar todos os por meio da CLI: *databricks fs ls dbfs:/FileStore/job-jars*

### <a name="or-you-can-use-the-databricks-cli"></a>Ou você pode usar a CLI do databricks:

1. Seguir [copiar a biblioteca usando a CLI do databricks](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs)

2. Usar a CLI do databricks [(etapas de instalação)](/azure/databricks/dev-tools/cli/#install-the-cli)

   Por exemplo, para copiar um JAR para dBFS: `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`
