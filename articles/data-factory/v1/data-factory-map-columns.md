---
title: Mapear colunas de conjunto de dados no Azure Data Factory | Microsoft Docs
description: Saiba como mapear colunas de origem para colunas de destino.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f97e41b9ee614da7ca852a15f101da1c91c86efb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Mapear colunas de conjunto de dados de origem para colunas de conjunto de dados de destino
O mapeamento de coluna pode ser usado para definir como colunas especificadas na "estrutura" da tabela de origem estão correlacionadas a colunas especificada na "estrutura" da tabela de coletor. A propriedade **columnMapping** está disponível na seção **typeProperties** da atividade Copiar.

O mapeamento de coluna oferece suporte para os seguintes cenários:

* Todas as colunas na estrutura do conjunto de dados de origem são mapeadas para todas as colunas na estrutura do conjunto de dados do coletor.
* Um subconjunto das colunas na estrutura do conjunto de dados de origem é mapeado para todas as colunas na estrutura do conjunto de dados do coletor.

Veja a seguir condições de erro que resultam em uma exceção:

* Menos colunas ou mais colunas na "estrutura" da tabela de coletor do que o especificado no mapeamento.
* Mapeamento duplicado.
* O resultado da consulta SQL não tem um nome de coluna especificado no mapeamento.

> [!NOTE]
> Os exemplos a seguir são para o Azure SQL e os Blobs do Azure, mas são aplicáveis a qualquer repositório de dados com suporte a conjuntos de dados retangulares. Ajuste o conjunto de dados e as definições de serviço vinculado nos exemplos para apontar para dados na fonte de dados relevante.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Exemplo 1 - mapeamento de coluna do SQL Azure para blobs do Azure
Neste exemplo, a tabela de entrada tem uma estrutura e ela aponta para uma tabela do SQL em um banco de dados SQL Azure.

```json
{
    "name": "AzureSQLInput",
    "properties": {
        "structure": 
         [
           { "name": "userid"},
           { "name": "name"},
           { "name": "group"}
         ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Neste exemplo, a tabela de saída tem uma estrutura e ela aponta para um blob em um armazenamento de blobs do Azure.

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
         "structure": 
          [
                { "name": "myuserid"},
                { "name": "myname" },
                { "name": "mygroup"}
          ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

O JSON a seguir define uma atividade de cópia em um pipeline. As colunas da fonte são mapeadas para colunas no coletor (**columnMappings**) utilizando a propriedade **Translator**.

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "Copy",
    "inputs":  [ { "name": "AzureSQLInput"  } ],
    "outputs":  [ { "name": "AzureBlobOutput" } ],
    "typeProperties":    {
        "source":
        {
            "type": "SqlSource"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    },
   "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
**Fluxo de mapeamento de coluna:**

![Fluxo de mapeamento de coluna](./media/data-factory-map-columns/column-mapping-flow.png)

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Exemplo 2 - mapeamento de coluna com a consulta SQL do SQL Azure para blobs do Azure
Neste exemplo, uma consulta SQL é usada para extrair dados do SQL Azure, em vez de simplesmente especificar o nome da tabela e os nomes das colunas na seção de "estrutura". 

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "CopyActivity",
    "inputs":  [ { "name": " AzureSQLInput"  } ],
    "outputs":  [ { "name": " AzureBlobOutput" } ],
    "typeProperties":
    {
        "source":
        {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "Translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
        }
    },
    "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
Nesse caso, os resultados da consulta primeiro são mapeados para colunas especificadas na "estrutura" da origem. Em seguida, as colunas da "estrutura" de origem são mapeadas para colunas na "estrutura" do coletor com as regras especificadas em columnMappings.  Suponha que a consulta retorne cinco colunas, duas colunas adicionais e as especificadas na "estrutura" de origem.

**Fluxo de mapeamento de coluna**

![Fluxo de mapeamento de coluna-2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>Próximas etapas
Veja o artigo para obter um tutorial sobre como usar a Atividade de Cópia: 

- [Copiar dados do Armazenamento de Blobs para o Banco de Dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
