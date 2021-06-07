---
title: Transformar dados com o JAR do databricks
description: Saiba como processar ou transformar dados executando um jar do databricks em um pipeline Azure Data Factory.
ms.service: data-factory
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
ms.date: 02/10/2021
ms.openlocfilehash: ccfe8fbf330e1c7f6f415b64a1f18d93a084a0ba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100374007"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Transformar dados executando uma atividade de Jar no Azure Databricks

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A atividade de Jar do Azure Databricks em um [pipeline do Data Factory](concepts-pipelines-activities.md) executa um Spark Jar em seu cluster do Azure Databricks. Este artigo se baseia no artigo sobre [atividades de transformação de dados](transform-data.md) que apresenta uma visão geral da transformação de dados e as atividades de transformação permitidas. O Azure Databricks é uma plataforma gerenciada para executar o Apache Spark.

Para ver uma introdução de 11 minutos e uma demonstração desse recurso, assista ao seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Definição de atividade do Databricks Jar

Aqui está a definição de JSON de exemplo de uma atividade de jar do databricks:

```json
{
    "name": "SparkJarActivity",
    "type": "DatabricksSparkJar",
    "linkedServiceName": {
        "referenceName": "AzureDatabricks",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mainClassName": "org.apache.spark.examples.SparkPi",
        "parameters": [ "10" ],
        "libraries": [
            {
                "jar": "dbfs:/docs/sparkpi.jar"
            }
        ]
    }
}

```

## <a name="databricks-jar-activity-properties"></a>Propriedades de atividade do Databricks Jar

A tabela a seguir descreve as propriedades JSON usadas na definição de JSON:

|Propriedade|Descrição|Obrigatório|
|:--|---|:-:|
|name|Nome da atividade no pipeline.|Sim|
|descrição|Texto que descreve o que a atividade faz.|Não|
|type|Para a Atividade do Databricks Jar, o tipo de atividade é DatabricksSparkJar.|Sim|
|linkedServiceName|Nome do serviço vinculado ao Databricks no qual a atividade de Jar é executado. Para saber mais sobre esse serviço vinculado, consulte o artigo [Compute linked services](compute-linked-services.md) (Serviços de computação vinculados).|Sim|
|mainClassName|O nome completo da classe que contém o método principal a ser executado. Essa classe deve estar contida em um JAR fornecido como uma biblioteca. Um arquivo JAR pode conter várias classes. Cada uma das classes pode conter um método Main.|Sim|
|parameters|Parâmetros que serão passados ao método principal. Essa propriedade é uma matriz de cadeias de caracteres.|Não|
|bibliotecas|Uma lista de bibliotecas a serem instaladas no cluster, que executará o trabalho. Ele pode ser uma matriz de <string, object>|Sim (pelo menos um contendo o método mainClassName)|

> [!NOTE]
> **Problema conhecido** – ao usar o mesmo [cluster interativo](compute-linked-services.md#example---using-existing-interactive-cluster-in-databricks) para executar atividades do jar do databricks simultâneos (sem a reinicialização do cluster), há um problema conhecido no databricks em que os parâmetros da 1ª atividade também serão usados pelas atividades a seguir. Portanto, isso resulta em parâmetros incorretos que estão sendo passados para os trabalhos subsequentes. Para atenuar isso, use um [cluster de trabalho](compute-linked-services.md#example---using-new-job-cluster-in-databricks) .

## <a name="supported-libraries-for-databricks-activities"></a>Bibliotecas com suporte para atividades do databricks

Na definição da atividade do databricks anterior, você especificou esses tipos de biblioteca:,,,, `jar` `egg` `maven` `pypi` `cran` .

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

Para obter mais informações, consulte a [documentação do databricks](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary) para tipos de biblioteca.

## <a name="how-to-upload-a-library-in-databricks"></a>Como carregar uma biblioteca no Databricks

### <a name="you-can-use-the-workspace-ui"></a>Você pode usar a interface do usuário do espaço de trabalho:

1. [Usar a interface do usuário do databricks Workspace](/azure/databricks/libraries/#create-a-library)

2. Para obter o caminho dBFS da biblioteca adicionada usando a interface do usuário, você pode usar a [CLI do databricks](/azure/databricks/dev-tools/cli/#install-the-cli).

   Normalmente, as bibliotecas Jar são armazenadas em dbfs:/FileStore/jars ao usar a interface do usuário. Você pode listar todos os por meio da CLI: *databricks fs ls dbfs:/FileStore/job-jars*

### <a name="or-you-can-use-the-databricks-cli"></a>Ou você pode usar a CLI do databricks:

1. Seguir [copiar a biblioteca usando a CLI do databricks](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs)

2. Usar a CLI do databricks [(etapas de instalação)](/azure/databricks/dev-tools/cli/#install-the-cli)

   Por exemplo, para copiar um JAR para dBFS: `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`

## <a name="next-steps"></a>Próximas etapas

Para ver uma introdução e uma demonstração desse recurso, Assista ao [vídeo](https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player).
