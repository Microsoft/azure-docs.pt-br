---
title: Atividade de pesquisa no Azure Data Factory | Microsoft Docs
description: "Saiba como usar a atividade de pesquisa para procurar um valor de uma fonte externa. Essa saída pode referenciada pelas atividades com êxito."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: shlo
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: spelluru
ms.openlocfilehash: 30173f8eea2ccbbcd44018596cf34b3769a64b50
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="lookup-activity-in-azure-data-factory"></a>Atividade de pesquisa no Azure Data Factory
A atividade de pesquisa pode ser usada para ler ou procurar um registro/nome de tabela/valor de qualquer fonte externa. Essa saída pode referenciada pelas atividades com êxito. 

Atualmente, há suporte às seguintes fontes de dados para a pesquisa:
- Arquivo JSON no Blob do Azure
- Arquivo JSON local
- Banco de Dados SQL do Azure (dados JSON convertidos da consulta)
- Armazenamento de Tabelas do Azure (dados JSON convertidos da consulta)

A atividade de pesquisa é útil quando você deseja recuperar dinamicamente uma lista de arquivos/registros/tabelas de um arquivo de configuração ou uma fonte de dados. A saída da atividade pode ser mais usada por outras atividades para executar o processamento específico apenas nesses itens.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está com GA (disponibilidade geral), consulte a [Documentação do Data Factory V1](v1/data-factory-introduction.md).


## <a name="example"></a>Exemplo
Neste exemplo, a atividade de cópia copia dados de uma tabela SQL em um Banco de Dados SQL do Azure do Armazenamento de Blobs do Azure. O nome da tabela SQL é armazenado em um arquivo JSON no Armazenamento de Blob. A atividade de pesquisa procura o nome da tabela em tempo de execução. Essa abordagem permite que o JSON seja modificado dinamicamente sem reimplantar pipelines/conjuntos de dados. 

### <a name="pipeline"></a>Pipeline
Esse pipeline contém duas atividades: **pesquisa** e **cópia**. 

- A atividade de pesquisa está configurada para usar o LookupDataset, que se refere a um local em um Armazenamento de Blobs do Azure. A atividade de pesquisa lê o nome da tabela SQL de um arquivo JSON neste local. 
- A atividade de cópia usa a saída da atividade de pesquisa (nome da tabela SQL). O tableName no conjunto de dados de origem (SourceDataset) está configurado para usar a saída da atividade de pesquisa. A atividade de cópia copia dados da tabela SQL para um local no Armazenamento de Blobs do Azure que é especificado pelo SinkDataset. 


```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
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
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.tableName}" 
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
O conjunto de dados de pesquisa refere-se ao arquivo sourcetable.json na pasta de pesquisa no Armazenamento do Azure especificado pelo AzureStorageLinkedService. 

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

### <a name="source-dataset-for-the-copy-activity"></a>Conjunto de dados de origem da atividade de cópia
O conjunto de dados de origem usa a saída da atividade de pesquisa, que é o nome da tabela SQL. A atividade de cópia copia dados dessa tabela SQL para um local no Armazenamento de Blobs do Azure especificado pelo conjunto de dados do coletor. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-the-copy-activity"></a>Conjunto de dados do coletor da atividade de cópia
A atividade de cópia copia dados da tabela SQL para o arquivo filebylookup.csv na pasta csv no Armazenamento do Azure especificado pelo AzureStorageLinkedService. 

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
Esta conta de armazenamento contém o arquivo JSON com os nomes de tabelas SQL. 

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
Este Banco de Dados SQL do Azure contém os dados a serem copiados para o armazenamento de blobs. 

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
  "tableName": "Table1",
}
{
   "Id": "2",
  "tableName": "Table2",
}
```
#### <a name="array-of-objects"></a>Matriz de objetos

```json
[ 
    {
        "Id": "1",
          "tableName": "Table1",
    }
    {
        "Id": "2",
        "tableName": "Table2",
    }
]
```



## <a name="type-properties"></a>Propriedades de tipo
Nome | Descrição | Tipo | Obrigatório
---- | ----------- | ---- | --------
conjunto de dados | O atributo de conjunto de dados tem como objetivo fornecer a referência de conjunto de dados para a pesquisa. Atualmente, os tipos de conjunto de dados com suporte são:<ul><li>FileShareDataset</li><li>AzureBlobDataset</li><li>AzureSqlTableDataset</li><li>AzureTableDataset</li> | pares chave/valor | Sim
fonte | Propriedades de origem específicas do banco de dados, mesmas que da origem da atividade de cópia | Pares chave/valor | Não
firstRowOnly | Retorna a primeira linha ou todas as linhas. | Booliano | Não

## <a name="next-steps"></a>Próximas etapas
Consulte outras atividades de fluxo de controle com suporte pelo Data Factory: 

- [Atividade de execução de pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade de obtenção de metadados](control-flow-get-metadata-activity.md)
- [Atividade da Web](control-flow-web-activity.md)
