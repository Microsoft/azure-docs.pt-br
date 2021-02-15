---
title: Mapeando colunas do conjunto de linhas no Azure Data Factory
description: Saiba como mapear colunas de origem para colunas de destino.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: fef2c6f120ae25e6aa1846d4971ff707da9bab92
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371117"
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Mapear colunas de conjunto de dados de origem para colunas de conjunto de dados de destino
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. 

O mapeamento de coluna pode ser usado para especificar como as colunas especificadas na "estrutura" da tabela de origem são mapeadas para as colunas especificadas na "estrutura" da tabela de coletor. A propriedade **columnMapping** está disponível na seção **typeProperties** da atividade Copiar.

O mapeamento de coluna oferece suporte para os seguintes cenários:

* Todas as colunas na estrutura do conjunto de dados de origem são mapeadas para todas as colunas na estrutura do conjunto de dados do coletor.
* Um subconjunto das colunas na estrutura do conjunto de dados de origem é mapeado para todas as colunas na estrutura do conjunto de dados do coletor.

Veja a seguir condições de erro que resultam em uma exceção:

* Uma quantidade menor de colunas ou mais colunas na "estrutura" da tabela de coletores do que o especificado no mapeamento.
* Mapeamento duplicado.
* O resultado da consulta SQL não tem um nome de coluna especificado no mapeamento.

> [!NOTE]
> Os exemplos a seguir são para o Azure SQL e os Blobs do Azure, mas são aplicáveis a qualquer repositório de dados com suporte a conjuntos de dados retangulares. Ajuste o conjunto de dados e as definições de serviço vinculado nos exemplos para apontar para dados na fonte de dados relevante.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Exemplo 1 - mapeamento de coluna do SQL Azure para blobs do Azure
Neste exemplo, a tabela de entrada tem uma estrutura e aponta para uma tabela SQL no banco de dados SQL do Azure.

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
Neste exemplo, uma consulta SQL é usada para extrair dados do SQL do Azure em vez de simplesmente especificar o nome da tabela e os nomes de coluna na seção "estrutura". 

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
Nesse caso, os resultados da consulta são mapeados primeiro para colunas especificadas em "Structure" da origem. Em seguida, as colunas da "estrutura" de origem são mapeadas para colunas na "estrutura" do coletor com as regras especificadas em columnMappings.  Suponha que a consulta retorne 5 colunas, mais duas colunas do que aquelas especificadas na "estrutura" da origem.

**Fluxo de mapeamento de coluna**

![Fluxo de mapeamento de coluna-2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>Próximas etapas
Veja o artigo para obter um tutorial sobre como usar a Atividade de Cópia: 

- [Copiar dados do Armazenamento de Blobs para o Banco de Dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
