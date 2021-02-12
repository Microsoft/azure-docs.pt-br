---
title: Atividade de pesquisa no Azure Data Factory
description: Saiba como usar a atividade Lookup para pesquisar um valor de uma fonte externa. Essa saída pode ser referenciada ainda mais pelas atividades com êxito.
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 66a17b61fef652160dc6d4a02bf330adbf0c7362
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006808"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Atividade de pesquisa no Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A atividade Lookup pode recuperar um conjunto de dados de quaisquer fontes de dados compatíveis com o Azure Data Factory. Use-a no seguinte cenário:
- Determinar dinamicamente em quais objetos operar em uma atividade subsequente, em vez de embutir o nome do objeto. Alguns exemplos de objeto são arquivos e tabelas.

A atividade Lookup lê e retorna o conteúdo de um arquivo de configuração ou tabela. Ela também retorna o resultado da execução de uma consulta ou procedimento armazenado. A saída da atividade Lookup pode ser usada em uma cópia subsequente ou atividade de transformação se for um valor de banco de dados individual. A saída poderá ser usada em uma atividade ForEach se for uma matriz de atributos.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

As seguintes fontes de dados são compatíveis com a atividade Lookup. 

A atividade de pesquisa pode retornar até 5000 linhas; Se o conjunto de resultados contiver mais registros, as primeiras 5000 linhas serão retornadas. A saída da atividade de pesquisa dá suporte a até 4 MB de tamanho, a atividade falhará se o tamanho exceder o limite. Atualmente, a duração mais longa para a atividade de pesquisa antes do tempo limite é de 24 horas.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>Sintaxe

```json
{
    "name":"LookupActivity",
    "type":"Lookup",
    "typeProperties":{
        "source":{
            "type":"<source type>"
        },
        "dataset":{
            "referenceName":"<source dataset name>",
            "type":"DatasetReference"
        },
        "firstRowOnly":<true or false>
    }
}
```

## <a name="type-properties"></a>Propriedades de tipo

Nome | Descrição | Type | Obrigatório?
---- | ----------- | ---- | --------
dataset | Fornece a referência de conjunto de dados para a pesquisa. Obtenha detalhes na seção **Propriedades do conjunto de dados** em cada artigo de conector correspondente. | Pares chave/valor | Sim
source | Contém propriedades de origem específicas do banco de dados, as mesmas que as da origem da atividade Copy. Obtenha detalhes na seção **Propriedades da atividade Copy** em cada artigo de conector correspondente. | Pares chave/valor | Sim
firstRowOnly | Indica se deve-se retornar apenas a primeira linha ou todas as linhas. | Boolean | Não. O padrão é `true`.

> [!NOTE]
> 
> * Não há suporte para as colunas de origem com o tipo **ByteArray**.
> * Não há suporte para **Estrutura** nas definições do conjunto de dados. Para arquivos de formato de texto, use a linha de cabeçalho para fornecer o nome da coluna.
> * Se sua origem da pesquisa for um arquivo JSON, não haverá suporte para a configuração `jsonPathDefinition` para remodelar o objeto JSON. Os objetos inteiros serão recuperados.

## <a name="use-the-lookup-activity-result"></a>Usar o resultado da atividade de pesquisa

O resultado de pesquisa é retornado na seção `output` do resultado da execução de atividade.

* **Quando `firstRowOnly` é definido como `true` (padrão)** , o formato de saída é como mostrado no código a seguir. O resultado da pesquisa fica sob um chave `firstRow` fixa. Para usar o resultado na atividade subsequente, use o padrão de  `@{activity('LookupActivity').output.firstRow.table}` .

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "schema":"dbo",
            "table":"Table1"
        }
    }
    ```

* **Quando `firstRowOnly` for definido como `false` (padrão)** , o formato de saída será como mostrado no código a seguir. Um campo `count` indica quantos registros são retornados. Os valores detalhados são exibidos em uma matriz `value` fixa. Nesse caso, a atividade Lookup é seguida por uma [atividade Foreach](control-flow-for-each-activity.md). Passe a matriz `value` para o campo `items` da atividade ForEach usando o padrão de `@activity('MyLookupActivity').output.value`. Para acessar elementos na matriz `value`, use a seguinte sintaxe: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. Um exemplo é `@{activity('lookupActivity').output.value[0].schema}`.

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "schema":"dbo",
                "table":"Table1"
            },
            {
                "Id": "2",
                "schema":"dbo",
                "table":"Table2"
            }
        ]
    } 
    ```

## <a name="example"></a>Exemplo

Neste exemplo, o pipeline contém duas atividades: **Lookup** e **Copy**. A atividade de cópia copia dados de uma tabela SQL em sua instância do banco de dados SQL do Azure para o armazenamento de BLOBs do Azure. O nome da tabela SQL é armazenado em um arquivo JSON em armazenamento de Blob. A atividade Lookup pesquisa o nome da tabela em runtime. O JSON é modificado dinamicamente usando essa abordagem. Não é necessário reimplantar pipelines ou conjuntos de dados. 

Este exemplo demonstra a pesquisa apenas para a primeira linha. Para pesquisar por todas as linhas e para encadear os resultados com a atividade ForEach, consulte os exemplos em [Copiar várias tabelas em massa usando o Azure Data Factory](tutorial-bulk-copy.md).


### <a name="pipeline"></a>Pipeline

- A atividade Lookup está configurada para usar **LookupDataset**, que se refere a um local no armazenamento de Blobs do Azure. A atividade Lookup lê o nome da tabela SQL de um arquivo JSON nesse local. 
- A atividade de cópia usa a saída da atividade de pesquisa, que é o nome da tabela SQL. A propriedade **tableName** no **SourceDataset** está configurada para usar a saída da atividade Lookup. A atividade Copy copia dados da tabela SQL para um local no armazenamento de Blobs do Azure. O local é especificado pela propriedade **SinkDataset**. 

```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "JsonSource",
                        "storeSettings": {
                            "type": "AzureBlobStorageReadSettings",
                            "recursive": true
                        },
                        "formatSettings": {
                            "type": "JsonReadSettings"
                        }
                    },
                    "dataset": {
                        "referenceName": "LookupDataset",
                        "type": "DatasetReference"
                    },
                    "firstRowOnly": true
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "dependsOn": [
                    {
                        "activity": "LookupActivity",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "AzureSqlSource",
                        "sqlReaderQuery": {
                            "value": "select * from [@{activity('LookupActivity').output.firstRow.schema}].[@{activity('LookupActivity').output.firstRow.table}]",
                            "type": "Expression"
                        },
                        "queryTimeout": "02:00:00",
                        "partitionOption": "None"
                    },
                    "sink": {
                        "type": "DelimitedTextSink",
                        "storeSettings": {
                            "type": "AzureBlobStorageWriteSettings"
                        },
                        "formatSettings": {
                            "type": "DelimitedTextWriteSettings",
                            "quoteAllText": true,
                            "fileExtension": ".txt"
                        }
                    },
                    "enableStaging": false,
                    "translator": {
                        "type": "TabularTranslator",
                        "typeConversion": true,
                        "typeConversionSettings": {
                            "allowDataTruncation": true,
                            "treatBooleanAsNumber": false
                        }
                    }
                },
                "inputs": [
                    {
                        "referenceName": "SourceDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "schemaName": {
                                "value": "@activity('LookupActivity').output.firstRow.schema",
                                "type": "Expression"
                            },
                            "tableName": {
                                "value": "@activity('LookupActivity').output.firstRow.table",
                                "type": "Expression"
                            }
                        }
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "SinkDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "schema": {
                                "value": "@activity('LookupActivity').output.firstRow.schema",
                                "type": "Expression"
                            },
                            "table": {
                                "value": "@activity('LookupActivity').output.firstRow.table",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        ],
        "annotations": [],
        "lastPublishTime": "2020-08-17T10:48:25Z"
    }
}
```

### <a name="lookup-dataset"></a>Conjunto de dados de pesquisa

O conjunto de **pesquisa** é o **sourcetable.jsno** arquivo na pasta de pesquisa do armazenamento do Azure especificada pelo tipo **AzureBlobStorageLinkedService** . 

```json
{
    "name": "LookupDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "Json",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": "sourcetable.json",
                "container": "lookup"
            }
        }
    }
}
```

### <a name="source-dataset-for-copy-activity"></a>Conjunto de dados de **origem** da atividade Copy

O conjunto de dados de **origem** usa a saída da atividade Lookup, que é o nome da tabela SQL. A atividade Copy copia dados dessa tabela SQL para um local no armazenamento de Blobs do Azure. O local é especificado pelo conjunto de dados do **coletor**. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureSqlDatabase",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "schemaName": {
                "type": "string"
            },
            "tableName": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "AzureSqlTable",
        "schema": [],
        "typeProperties": {
            "schema": {
                "value": "@dataset().schemaName",
                "type": "Expression"
            },
            "table": {
                "value": "@dataset().tableName",
                "type": "Expression"
            }
        }
    }
}
```

### <a name="sink-dataset-for-copy-activity"></a>Conjunto de dados do **coletor** da atividade Copy

A atividade Copy copia dados da tabela SQL para o arquivo **filebylookup.csv** na pasta **csv** no Armazenamento do Azure. O arquivo é especificado pela propriedade **AzureBlobStorageLinkedService** . 

```json
{
    "name": "SinkDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "schema": {
                "type": "string"
            },
            "table": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "DelimitedText",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": {
                    "value": "@{dataset().schema}_@{dataset().table}.csv",
                    "type": "Expression"
                },
                "container": "csv"
            },
            "columnDelimiter": ",",
            "escapeChar": "\\",
            "quoteChar": "\""
        },
        "schema": []
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

Você pode usar os dois tipos de formatos a seguir para **sourcetable.jsno** arquivo.

#### <a name="set-of-objects"></a>Conjunto de objetos

```json
{
   "Id":"1",
   "schema":"dbo",
   "table":"Table1"
}
{
   "Id":"2",
   "schema":"dbo",
   "table":"Table2"
}
```

#### <a name="array-of-objects"></a>Matriz de objetos

```json
[ 
    {
        "Id": "1",
        "schema":"dbo",
        "table":"Table1"
    },
    {
        "Id": "2",
        "schema":"dbo",
        "table":"Table2"
    }
]
```

## <a name="limitations-and-workarounds"></a>Limitações e Soluções Alternativas

Aqui estão algumas limitações da atividade de pesquisa e soluções alternativas sugeridas.

| Limitações | Solução alternativa |
|---|---|
| A atividade de pesquisa tem um máximo de 5.000 linhas e um tamanho máximo de 2 MB. | Crie um pipeline de dois níveis onde o pipeline externo itera em um pipeline interno que recupera os dados que não excedem o máximo de linhas ou o tamanho. |
| | |

## <a name="next-steps"></a>Próximas etapas
Consulte outras atividades de fluxo de controle com suporte pelo Data Factory: 

- [Atividade de execução de pipeline](control-flow-execute-pipeline-activity.md)
- [Atividade ForEach](control-flow-for-each-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
- [Atividade da Web](control-flow-web-activity.md)
