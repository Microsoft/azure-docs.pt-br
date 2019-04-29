---
title: Invocar o procedimento armazenado de atividade de cópia do Azure Data Factory | Microsoft Docs
description: Saiba como invocar um procedimento armazenado no Banco de Dados SQL do Azure ou SQL Server de uma atividade de cópia do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 35e9347039a7b9939ab4d2719f9738429dec168c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60824245"
---
# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>Invocar procedimento armazenado de atividade de cópia no Azure Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [transformar dados usando a atividade de procedimento armazenado no Data Factory ](../transform-data-using-stored-procedure.md).


Ao copiar dados no [SQL Server](data-factory-sqlserver-connector.md) ou [Banco de Dados SQL do Azure](data-factory-azure-sql-connector.md), você pode configurar o **SqlSink** na atividade de cópia para invocar um procedimento armazenado. Talvez você queira usar o procedimento armazenado para executar algum processamento adicional (mesclar colunas, pesquisar valores, inserção em várias tabelas, etc.) necessário antes de inserir dados na tabela de destino. Esse recurso se beneficia de [parâmetros com valores de tabela](https://msdn.microsoft.com/library/bb675163.aspx). 

A amostra a seguir mostra como invocar um procedimento armazenado em um banco de dados do SQL Server de um pipeline de Data Factory (atividade de cópia):  

## <a name="output-dataset-json"></a>JSON do conjunto de dados de saída
No JSON do conjunto de dados de saída, defina **type** como: **SqlServerTable**. Defina-o como **AzureSqlTable** para usá-lo com um Banco de Dados SQL do Azure. O valor da propriedade **tableName** deve corresponder ao nome do primeiro parâmetro do procedimento armazenado.  

```json
{
  "name": "SqlOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlLinkedService",
    "typeProperties": {
      "tableName": "Marketing"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

## <a name="sqlsink-section-in-copy-activity-json"></a>Seção SqlSink na atividade de cópia JSON
Defina a seção **SqlSink** na atividade de cópia JSON conforme demonstrado a seguir. Para invocar um procedimento armazenado ao inserir dados no banco de dados do coletor/destino, especifique valores para ambas as propriedades **SqlWriterStoredProcedureName** e **SqlWriterTableType**. Para obter descrições dessas propriedades, consulte a [seção SqlSink no artigo de conector do SQL Server](data-factory-sqlserver-connector.md#sqlsink).

```json
"sink":
{
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
    "storedProcedureParameters":
            {
                "stringData": 
                {
                    "value": "str1"     
                }
            }
}
```

## <a name="stored-procedure-definition"></a>Definição do procedimento armazenado 
No banco de dados, defina o procedimento armazenado com o mesmo nome que **SqlWriterStoredProcedureName**. O procedimento armazenado manipula dados de entrada do armazenamento de dados de origem e insere dados em uma tabela no banco de dados de destino. O nome do primeiro parâmetro do procedimento armazenado deve corresponder ao tableName definido no JSON do conjunto de dados (Marketing).

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
AS
BEGIN
    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    INSERT [dbo].[Marketing](ProfileID, State)
    SELECT * FROM @Marketing
END
```

## <a name="table-type-definition"></a>Definição de tipo de tabela
No banco de dados, defina o tipo de tabela com o mesmo nome que **SqlWriterTableType**. O esquema do tipo de tabela deve corresponder ao esquema de conjunto de dados de entrada.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>Próximas etapas
Examine os artigos sobre conector a seguir para obter exemplos de JSON completos: 

- [Banco de Dados SQL do Azure](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
