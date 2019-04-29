---
title: Atividade de pesquisa no Azure Data Factory | Microsoft Docs
description: Saiba como usar a atividade Lookup para pesquisar um valor de uma fonte externa. Essa saída pode ser referenciada ainda mais pelas atividades com êxito.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: shlo
ms.openlocfilehash: 4f0662a71ee14af3c2c1aafee210641fc8b51f1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60768642"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Atividade de pesquisa no Azure Data Factory

A atividade Lookup pode recuperar um conjunto de dados de quaisquer fontes de dados compatíveis com o Azure Data Factory. Use-a no seguinte cenário:
- Determinar dinamicamente em quais objetos operar em uma atividade subsequente, em vez de embutir o nome do objeto. Alguns exemplos de objeto são arquivos e tabelas.

A atividade Lookup lê e retorna o conteúdo de um arquivo de configuração ou tabela. Ela também retorna o resultado da execução de uma consulta ou procedimento armazenado. A saída da atividade Lookup pode ser usada em uma cópia subsequente ou atividade de transformação se for um valor de banco de dados individual. A saída poderá ser usada em uma atividade ForEach se for uma matriz de atributos.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

As seguintes fontes de dados são compatíveis com a atividade Lookup. O maior número de linhas que podem ser retornadas pela atividade Lookup é 5 mil, de até 2 MB em tamanho. No momento, a maior duração da atividade Lookup antes de atingir o tempo limite é uma hora.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>Sintaxe

```json
{
    "name": "LookupActivity",
    "type": "Lookup",
    "typeProperties": {
        "source": {
            "type": "<source type>"
            <additional source specific properties (optional)>
        },
        "dataset": { 
            "referenceName": "<source dataset name>",
            "type": "DatasetReference"
        },
        "firstRowOnly": false
    }
}
```

## <a name="type-properties"></a>Propriedades de tipo

NOME | DESCRIÇÃO | Type | Obrigatório?
---- | ----------- | ---- | --------
dataset | Fornece a referência de conjunto de dados para a pesquisa. Obtenha detalhes na seção **Propriedades do conjunto de dados** em cada artigo de conector correspondente. | Pares chave/valor | Sim
fonte | Contém propriedades de origem específicas do banco de dados, as mesmas que as da origem da atividade Copy. Obtenha detalhes na seção **Propriedades da atividade Copy** em cada artigo de conector correspondente. | Pares chave/valor | Sim
firstRowOnly | Indica se deve-se retornar apenas a primeira linha ou todas as linhas. | Boolean | Não. O padrão é `true`.

> [!NOTE]
> 
> * Não há suporte para as colunas de origem com o tipo **ByteArray**.
> * Não há suporte para **Estrutura** nas definições do conjunto de dados. Para arquivos de formato de texto, use a linha de cabeçalho para fornecer o nome da coluna.
> * Se sua origem da pesquisa for um arquivo JSON, não haverá suporte para a configuração `jsonPathDefinition` para remodelar o objeto JSON. Os objetos inteiros serão recuperados.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Use o resultado da atividade Lookup em uma atividade subsequente

O resultado de pesquisa é retornado na seção `output` do resultado da execução de atividade.

* **Quando `firstRowOnly` é definido como `true` (padrão)**, o formato de saída é como mostrado no código a seguir. O resultado da pesquisa fica sob um chave `firstRow` fixa. Para usar o resultado na atividade subsequente, use o padrão de `@{activity('MyLookupActivity').output.firstRow.TableName}`.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **Quando `firstRowOnly` for definido como `false` (padrão)**, o formato de saída será como mostrado no código a seguir. Um campo `count` indica quantos registros são retornados. Os valores detalhados são exibidos em uma matriz `value` fixa. Nesse caso, a atividade Lookup é seguida por uma [atividade Foreach](control-flow-for-each-activity.md). Passe a matriz `value` para o campo `items` da atividade ForEach usando o padrão de `@activity('MyLookupActivity').output.value`. Para acessar elementos na matriz `value`, use a seguinte sintaxe: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. Um exemplo é `@{activity('lookupActivity').output.value[0].tablename}`.

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "TableName" : "Table1"
            },
            {
                "Id": "2",
                "TableName" : "Table2"
            }
        ]
    } 
    ```

### <a name="copy-activity-example"></a>Exemplo da atividade Copy
Neste exemplo, a atividade Copy copia dados de uma tabela SQL em uma instância do Banco de Dados SQL do Azure para o Armazenamento de Blobs do Azure. O nome da tabela SQL é armazenado em um arquivo JSON em armazenamento de Blob. A atividade Lookup pesquisa o nome da tabela em tempo de execução. O JSON é modificado dinamicamente usando essa abordagem. Não é necessário reimplantar pipelines ou conjuntos de dados. 

Este exemplo demonstra a pesquisa apenas para a primeira linha. Para pesquisar por todas as linhas e para encadear os resultados com a atividade ForEach, consulte os exemplos em [Copiar várias tabelas em massa usando o Azure Data Factory](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Pipeline
Esse pipeline contém duas atividades: Pesquisa e cópia. 

- A atividade Lookup está configurada para usar **LookupDataset**, que se refere a um local no armazenamento de Blobs do Azure. A atividade Lookup lê o nome da tabela SQL de um arquivo JSON nesse local. 
- A atividade Copy usa a saída da atividade Lookup, que é o nome da tabela SQL. A propriedade **tableName** no **SourceDataset** está configurada para usar a saída da atividade Lookup. A atividade Copy copia dados da tabela SQL para um local no armazenamento de Blobs do Azure. O local é especificado pela propriedade **SinkDataset**. 

```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.firstRow.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>Conjunto de dados de pesquisa
O conjunto de dados de **pesquisa** é o arquivo **sourcetable.json** na pasta de consulta do Armazenamento do Azure especificada pelo tipo **AzureStorageLinkedService**. 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
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
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.firstRow.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-copy-activity"></a>Conjunto de dados do **coletor** da atividade Copy
A atividade Copy copia dados da tabela SQL para o arquivo **filebylookup.csv** na pasta **csv** no Armazenamento do Azure. O arquivo é especificado pela propriedade **AzureStorageLinkedService**. 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Serviço vinculado de armazenamento do Azure
Esta conta de armazenamento contém o arquivo JSON com os nomes das tabelas SQL. 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>",
                "type": "SecureString"
            }
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Serviço vinculado para o Banco de Dados SQL do Azure
Esta instância de Banco de Dados SQL do Azure contém os dados a serem copiados para o armazenamento de Blobs. 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": {
                "value": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;",
                "type": "SecureString"
            }
        }
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

#### <a name="set-of-objects"></a>Conjunto de objetos

```json
{
  "Id": "1",
  "tableName": "Table1"
}
{
   "Id": "2",
  "tableName": "Table2"
}
```

#### <a name="array-of-objects"></a>Matriz de objetos

```json
[ 
    {
        "Id": "1",
        "tableName": "Table1"
    },
    {
        "Id": "2",
        "tableName": "Table2"
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
