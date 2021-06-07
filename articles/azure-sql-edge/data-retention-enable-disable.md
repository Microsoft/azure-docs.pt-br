---
title: Habilitar e desabilitar políticas de retenção de dados – Azure SQL Edge
description: Saiba como habilitar e desabilitar políticas de retenção de dados no Azure SQL Edge
keywords: Borda do SQL, retenção de dados
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: 5b8dd911952a63ba8775f27a6128ff61e849e823
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861418"
---
# <a name="enable-and-disable-data-retention-policies"></a>Habilitar e desabilitar políticas de retenção de dados

Este tópico descreve como habilitar e desabilitar políticas de retenção de dados para um banco de dado e uma tabela do. 

## <a name="enable-data-retention-for-a-database"></a>Habilitar a retenção de dados para um banco de dado

O exemplo a seguir mostra como habilitar a retenção de dados usando [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-set-options).

```sql
ALTER DATABASE [<DatabaseName>] SET DATA_RETENTION  ON;
```

## <a name="check-if-data-retention-is-enabled-for-a-database"></a>Verificar se a retenção de dados está ativada para um banco de dados

O comando a seguir pode ser usado para verificar se a retenção de dados está habilitada para um banco de dado
```sql
SELECT is_data_retention_enabled, name
FROM sys.databases;
```

## <a name="enable-data-retention-for-a-table"></a>Habilitar a retenção de dados para uma tabela

A retenção de dados deve ser habilitada para cada tabela para a qual você deseja que os dados sejam limpos automaticamente. Quando a retenção de dados estiver habilitada no banco de dado e na tabela, uma tarefa de sistema em segundo plano verificará periodicamente a tabela para identificar e excluir quaisquer linhas obsoletas (antigas). A retenção de dados pode ser habilitada em uma tabela durante a criação da tabela usando [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) ou usando [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql).

O exemplo a seguir mostra como habilitar a retenção de dados para uma tabela usando [criar tabela](/sql/t-sql/statements/create-table-transact-sql). 

```sql
CREATE TABLE [dbo].[data_retention_table] 
(
[dbdatetime2] datetime2(7), 
[product_code] int, 
[value] char(10),  
CONSTRAINT [pk_current_data_retention_table] PRIMARY KEY CLUSTERED ([product_code])
) WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )
```

A `WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )` parte do comando criar tabela define a retenção de dados na tabela. O comando usa os seguintes parâmetros necessários 

- DATA_DELETION-indica se a retenção de dados está ativada ou desativada.
- FILTER_COLUMN-Name na coluna da tabela, que será usado para verificar se as linhas estão obsoletas ou não. A coluna de filtro só pode ser uma coluna com os seguintes tipos de dados 
    - Data
    - SmallDateTime
    - Datetime
    - DateTime2
    - DateTimeOffset
- RETENTION_PERIOD-um valor inteiro seguido por um descritor de unidade. As unidades permitidas são dia, dias, semana, semanas, mês, meses, ano e anos.

O exemplo a seguir mostra como habilitar a retenção de dados para a tabela usando [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql).  

```sql
Alter Table [dbo].[data_retention_table]
SET (DATA_DELETION = On (FILTER_COLUMN = [timestamp], RETENTION_PERIOD = 1 day))
```

## <a name="check-if-data-retention-is-enabled-for-a-table"></a>Verificar se a retenção de dados está habilitada para uma tabela

O comando a seguir pode ser usado para verificar as tabelas para as quais a retenção de dados está habilitada

```sql
select name, data_retention_period, data_retention_period_unit from sys.tables
```

Um valor de data_retention_period =-1 e data_retention_period_unit como INFINITOs indica que a retenção de dados não está definida na tabela.

A consulta a seguir pode ser usada para identificar a coluna usada como a filter_column para a retenção de dados. 

```sql
Select name from sys.columns
where is_data_deletion_filter_column =1 
and object_id = object_id(N'dbo.data_retention_table', N'U')
```

## <a name="correlating-db-and-table-data-retention-settings"></a>Correlacionando configurações de retenção de banco de dados e tabela

A configuração de retenção de dados no banco de dado e na tabela são usadas em conjunto para determinar se a limpeza automática para linhas antigas será executada nas tabelas ou não. 

|Opção de banco de dados | Opção de tabela | Comportamento |
|----------------|--------------|----------|
| OFF | OFF | A política de retenção de dados está desabilitada e a limpeza automática e manual de registros antigos está desabilitada.|
| OFF | ATIVADO  | A política de retenção de dados está habilitada para a tabela. A limpeza automática de registros obsoletos está desabilitada, mas o método de limpeza manual pode ser usado para limpar registros obsoletos. |
| ATIVADO | OFF | A política de retenção de dados está habilitada no nível do banco de dado. No entanto, como a opção está desabilitada no nível de tabela, não há nenhuma limpeza baseada em retenção de linhas antigas.|
| ATIVADO | ATIVADO | A política de retenção de dados está habilitada para o banco de dados e as tabelas. A limpeza automática de registros obsoletos está habilitada. |

## <a name="disable-data-retention-on-a-table"></a>Desabilitar a retenção de dados em uma tabela 

A retenção de dados pode ser desabilitada em uma tabela usando [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql). O comando a seguir pode ser usado para desabilitar a retenção de dados em uma tabela.

```sql
Alter Table [dbo].[data_retention_table]
Set (DATA_DELETION = OFF)
```

## <a name="disable-data-retention-on-a-database"></a>Desabilitar a retenção de dados em um banco de dado

A retenção de dados pode ser desabilitada em uma tabela usando [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-set-options). O comando a seguir pode ser usado para desabilitar a retenção de dados em um banco de dado.

```sql
ALTER DATABASE <DatabaseName> SET DATA_RETENTION  OFF;
```

## <a name="next-steps"></a>Próximas etapas
- [Retenção de dados e limpeza automática de dados](data-retention-overview.md)
- [Gerenciar dados históricos com a política de retenção](data-retention-cleanup.md)
