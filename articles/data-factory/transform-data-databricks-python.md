---
title: Transformar dados com o Databricks Python
description: Saiba como processar ou transformar dados executando uma atividade de Python do Databricks em um pipeline do Azure Data Factory.
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/15/2018
author: dcstwh
ms.author: weetok
ms.custom: devx-track-python
ms.openlocfilehash: 49dfe11ceb01471e3b5afadd30259dcd63e7b82a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373939"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Transformar dados executando uma atividade de Python no Azure Databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A atividade de Python do Azure Databricks em um [pipeline do Data Factory](concepts-pipelines-activities.md) executa um arquivo de Python em seu cluster do Azure Databricks. Este artigo se baseia no artigo sobre [atividades de transformação de dados](transform-data.md) que apresenta uma visão geral da transformação de dados e as atividades de transformação permitidas. O Azure Databricks é uma plataforma gerenciada para executar o Apache Spark.

Para ver uma introdução de 11 minutos e uma demonstração desse recurso, assista ao seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Definição de atividade do Databricks Python

A seguir está a definição JSON de exemplo de uma Atividade de Python do Databricks:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksSparkPython",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "pythonFile": "dbfs:/docs/pi.py",
            "parameters": [
                "10"
            ],
            "libraries": [
                {
                    "pypi": {
                        "package": "tensorflow"
                    }
                }
            ]
        }
    }
}
```

## <a name="databricks-python-activity-properties"></a>Propriedades de atividade do Databricks Python

A tabela a seguir descreve as propriedades JSON usadas na definição de JSON:

|Propriedade|Descrição|Obrigatório|
|---|---|---|
|name|Nome da atividade no pipeline.|Sim|
|descrição|Texto que descreve o que a atividade faz.|Não|
|type|Para a Atividade do Databricks Python, o tipo de atividade é DatabricksSparkPython.|Sim|
|linkedServiceName|Nome do serviço vinculado ao Databricks no qual a atividade de Python é executado. Para saber mais sobre esse serviço vinculado, consulte o artigo [Compute linked services](compute-linked-services.md) (Serviços de computação vinculados).|Sim|
|pythonFile|O URI do arquivo Python a ser executado. Há suporte para apenas os caminhos DBFS.|Sim|
|parameters|Parâmetros de linha de comando que serão passados para o arquivo Python. Isto é uma matriz de cadeias de caracteres.|Não|
|bibliotecas|Uma lista de bibliotecas a serem instaladas no cluster, que executará o trabalho. Ele pode ser uma matriz de <string, object>|Não|

## <a name="supported-libraries-for-databricks-activities"></a>Bibliotecas com suporte para atividades do databricks

Na definição da atividade acima do Databricks você especifica esses tipos de biblioteca: *jar*, *egg*, *maven*, *pypi*, *cran*.

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

Para obter mais detalhes, consulte [documentação do Databricks](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary) para tipos de biblioteca.

## <a name="how-to-upload-a-library-in-databricks"></a>Como carregar uma biblioteca no Databricks

### <a name="you-can-use-the-workspace-ui"></a>Você pode usar a interface do usuário do Workspace:

1. [Usar a interface do usuário do workspace do Databricks](/azure/databricks/libraries/#create-a-library)

2. Para obter o caminho dbfs da biblioteca adicionada usando a interface do usuário, você pode usar a [CLI do Databricks](/azure/databricks/dev-tools/cli/#install-the-cli).

   Normalmente, as bibliotecas Jar são armazenadas em dbfs:/FileStore/jars ao usar a interface do usuário. Você pode listar todos os por meio da CLI: *databricks fs ls dbfs:/FileStore/job-jars*

### <a name="or-you-can-use-the-databricks-cli"></a>Ou você pode usar a CLI do Databricks:

1. Siga [Copie a biblioteca usando a CLI do Databricks](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs)

2. Usar a CLI do Databricks [(etapas de instalação)](/azure/databricks/dev-tools/cli/#install-the-cli)

   Por exemplo, para copiar um JAR para dbfs: `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`
