---
title: Sincronizar o Apache Spark para definições de tabela externa no pool de SQL sem servidor
description: Visão geral de como consultar tabelas do Spark usando o pool de SQL sem servidor
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 057a69881b8b407e5d75fa3510ca1c3eb1830bc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96446496"
---
# <a name="synchronize-apache-spark-for-azure-synapse-external-table-definitions-in-serverless-sql-pool"></a>Sincronizar o Apache Spark para definições de tabela externa do Azure Synapse no pool de SQL sem servidor

O pool de SQL sem servidor pode sincronizar automaticamente metadados do Apache Spark. Um banco de dados do pool de SQL sem servidor será criado para cada banco de dados existente nos pools do Apache Spark sem servidor. 

Para cada tabela externa do Spark baseada em Parquet e localizada no Armazenamento do Azure, uma tabela externa é criada no banco de dados do pool de SQL sem servidor. Assim, você pode desligar os seus pools do Spark e ainda consultar tabelas externas do Spark no pool de SQL sem servidor.

Quando uma tabela é particionada no Spark, os arquivos do armazenamento são organizados por pastas. O pool de SQL sem servidor usará metadados de partição e apenas terá como destino pastas e arquivos relevantes para a consulta.

A sincronização de metadados é configurada automaticamente para cada Pool do Apache Spark sem servidor provisionado no workspace do Azure Synapse. Você pode começar a consultar as tabelas externas do Spark instantaneamente.

Cada tabela externa de parquet do Spark localizada no Armazenamento do Azure é representada por uma tabela externa em um esquema dbo que corresponde a um banco de dados do pool de SQL sem servidor. 

Para consultas de tabela externa do Spark, execute uma consulta que tenha como destino uma [tabela_do_Spark] externa. Antes de executar o exemplo a seguir, verifique se você tem o [acesso correto à conta de armazenamento](develop-storage-files-storage-access-control.md) na qual os arquivos estão localizados.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

> [!NOTE]
> Adicionar, remover ou alterar os comandos de tabela externa do Spark em uma coluna não refletirá na tabela externa no pool de SQL sem servidor.

## <a name="apache-spark-data-types-to-sql-data-types-mapping"></a>Mapeamento de tipos de dados do Apache Spark para tipos de dados SQL

| Tipo de dados do Spark | Tipo de dados SQL               |
| --------------- | --------------------------- |
| ByteType        | SMALLINT                    |
| Tipo curto      | SMALLINT                    |
| IntegerType     | INT                         |
| LongType        | BIGINT                      |
| FloatType       | real                        |
| DoubleType      | FLOAT                       |
| DecimalType     | decimal                     |
| TimestampType   | datetime2                   |
| DateType        | date                        |
| StringType      | varchar(max)\*               |
| BinaryType      | varbinary                   |
| BooleanType     | bit                         |
| ArrayType       | varchar(max)\* (no JSON)\** |
| MapType         | varchar(max)\* (no JSON)\** |
| StructType      | varchar(max)\* (no JSON)\** |

\* A ordenação usada é Latin1_General_100_BIN2_UTF8.

\** ArrayType, MapType e StructType são representados como JSONs.



## <a name="next-steps"></a>Próximas etapas

Avance para o artigo [Controle de acesso de armazenamento](develop-storage-files-storage-access-control.md) para saber mais sobre o controle de acesso de armazenamento.
