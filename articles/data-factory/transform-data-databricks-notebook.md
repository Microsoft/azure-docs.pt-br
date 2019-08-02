---
title: Transformar dados com o Notebook do Databricks - Azure | Microsoft Docs
description: Saiba como processar ou transformar dados executando um notebook do Databricks.
services: data-factory
documentationcenter: ''
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/15/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 2bc8b84d4b98036acc93788dee88444786df139e
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335846"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Transformar dados executando um notebook do Databricks

A Atividade de Notebook do Azure Databricks em um [pipeline do Data Factory](concepts-pipelines-activities.md) executa um notebook do Databricks no workspace do Azure Databricks. Este documento se baseia no artigo sobre as  [atividades de transformação de dados](transform-data.md) , que apresenta uma visão geral da transformação de dados e das atividades de transformação permitidas. O Azure Databricks é uma plataforma gerenciada para executar o Apache Spark.

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
|description|Texto que descreve o que a atividade faz.|Não|
|type|Para Atividade de Notebook do Databricks, o tipo da atividade é DatabricksNotebook.|Sim|
|linkedServiceName|Nome do serviço vinculado do Databricks no qual o notebook do Databricks executa. Saiba mais sobre esse serviço vinculado no artigo  [Serviços de computação vinculados](compute-linked-services.md) .|Sim|
|notebookPath|O caminho absoluto do notebook a ser executado no workspace do Databricks. Esse caminho deve começar com uma barra.|Sim|
|baseParameters|Uma matriz de pares chave-valor. Parâmetros básicos podem ser utilizados para cada execução de atividade. Se o notebook utilizar um parâmetro que não for especificado, será usado o valor padrão do notebook. Encontre mais informações sobre os parâmetros em [Notebooks do Databricks](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Não|
|bibliotecas|Uma lista de bibliotecas a serem instaladas no cluster, que executará o trabalho. Pode ser uma matriz de \<cadeia de caracteres, object>.|Não|


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

Para obter mais detalhes, consulte a [documentação do Databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) para tipos de biblioteca.

## <a name="how-to-upload-a-library-in-databricks"></a>Como carregar uma biblioteca no Databricks

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Usando o workspace do Databricks da interface do usuário](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Para obter o caminho dbfs da biblioteca adicionada usando a interface do usuário, você pode usar a [CLI do Databricks (instalação)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Normalmente, as bibliotecas Jar são armazenadas em dbfs:/FileStore/jars ao usar a interface do usuário. Você pode listar todos os por meio da CLI: *databricks fs ls dbfs:/FileStore/jars*.



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Copie a biblioteca usando a CLI do Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Exemplo: *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*
