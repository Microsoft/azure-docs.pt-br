---
title: Usar procedimentos armazenados
description: Dicas para implementar procedimentos armazenados usando o Synapse SQL no Azure Synapse Analytics para desenvolvimento de soluções.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/03/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 083077b2b29bc415e83d6f5a76941fa83fa853e3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674152"
---
# <a name="stored-procedures-using-synapse-sql-in-azure-synapse-analytics"></a>Procedimentos armazenados usando o SQL Synapse no Azure Synapse Analytics

Os pools do Synapse SQL provisionados e sem servidor permitem que você coloque uma lógica complexa de processamento de dados em procedimentos armazenados do SQL. Os procedimentos armazenados são uma ótima maneira de encapsular seu código SQL e armazená-los perto dos seus dados no data warehouse. Os procedimentos armazenados ajudam os desenvolvedores a modularizar suas soluções encapsulando o código em unidades gerenciáveis e facilitando a maior Reusabilidade de código. Cada procedimento armazenado também pode aceitar parâmetros para torná-lo ainda mais flexível.
Neste artigo, você encontrará algumas dicas para implementar procedimentos armazenados no pool de SQL do Synapse para desenvolver soluções.

## <a name="what-to-expect"></a>O que esperar

Synapse SQL dá suporte a muitos dos recursos do T-SQL que são usados no SQL Server. Mais importante, há recursos específicos de expansão que você pode usar para maximizar o desempenho da sua solução. Neste artigo, você aprenderá sobre os recursos que podem ser colocados em procedimentos armazenados.

> [!NOTE]
> No corpo do procedimento, você pode usar apenas os recursos que têm suporte na área de superfície do SQL do Synapse. Examine [Este artigo](overview-features.md) para identificar objetos, instruções que podem ser usadas em procedimentos armazenados. Os exemplos nestes artigos usam recursos genéricos que estão disponíveis na área de superfície dedicada e sem servidor. Confira [limitações adicionais em pools do SQL Synapse e sem servidor](#limitations) no final deste artigo.

Para manter a escala e o desempenho do pool do SQL, há também alguns recursos e funcionalidades que têm diferenças comportamentais e outras que não têm suporte.

## <a name="stored-procedures-in-synapse-sql"></a>Procedimentos armazenados no SQL Synapse

No exemplo a seguir, você pode ver os procedimentos que removem objetos externos, se existirem no banco de dados:

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_tables WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL TABLE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_file_format_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_file_formats WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL FILE FORMAT ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_data_source_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_data_sources WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL DATA SOURCE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
```

Esses procedimentos podem ser executados usando `EXEC` a instrução em que você pode especificar o nome do procedimento e os parâmetros:

```sql
EXEC drop_external_table_if_exists 'mytest';
EXEC drop_external_file_format_if_exists 'mytest';
EXEC drop_external_data_source_if_exists 'mytest';
```

O Synapse SQL fornece uma implementação de procedimento armazenado simplificado e simplificado. A maior diferença em comparação ao SQL Server é que o procedimento armazenado não é um código pré-compilado. Em data warehouses, o tempo de compilação é pequeno em comparação com o tempo necessário para executar consultas em grandes volumes de dados. É mais importante garantir que o código do procedimento armazenado seja otimizado corretamente para grandes consultas. A meta é poupar horas, minutos e segundos, não milissegundos. Portanto, é mais útil pensar em procedimentos armazenados como contêineres para a lógica SQL.

Quando o Synapse SQL executa o procedimento armazenado, as instruções SQL são analisadas, traduzidas e otimizadas em tempo de execução. Durante esse processo, cada instrução é convertida em consultas distribuídas. O código SQL executado nos dados é diferente da consulta enviada.

## <a name="encapsulate-validation-rules"></a>Encapsular regras de validação

Os procedimentos armazenados permitem que você localize a lógica de validação em um único módulo armazenado no banco de dados SQL. No exemplo a seguir, você pode ver como validar os valores dos parâmetros e alterar seus valores padrão.

```sql
CREATE PROCEDURE count_objects_by_date_created 
                            @start_date DATETIME2,
                            @end_date DATETIME2
AS BEGIN 

    IF( @start_date >= GETUTCDATE() )
    BEGIN
        THROW 51000, 'Invalid argument @start_date. Value should be in past.', 1;  
    END

    IF( @end_date IS NULL )
    BEGIN
        SET @end_date = GETUTCDATE();
    END

    IF( @start_date >= @end_date )
    BEGIN
        THROW 51000, 'Invalid argument @end_date. Value should be greater than @start_date.', 2;  
    END

    SELECT
         year = YEAR(create_date),
         month = MONTH(create_date),
         objects_created = COUNT(*)
    FROM
        sys.objects
    WHERE
        create_date BETWEEN @start_date AND @end_date
    GROUP BY
        YEAR(create_date), MONTH(create_date);
END
```

A lógica no procedimento SQL validará os parâmetros de entrada quando o procedimento for chamado.

```sql

EXEC count_objects_by_date_created '2020-08-01', '2020-09-01'

EXEC count_objects_by_date_created '2020-08-01', NULL

EXEC count_objects_by_date_created '2020-09-01', '2020-08-01'
-- Error
-- Invalid argument @end_date. Value should be greater than @start_date.

EXEC count_objects_by_date_created '2120-09-01', NULL
-- Error
-- Invalid argument @start_date. Value should be in past.
```

## <a name="nesting-stored-procedures"></a>Aninhamento de procedimentos armazenados

Quando os procedimentos armazenados chamam outros procedimentos armazenados ou executam SQL dinâmico, a invocação interna de código ou procedimento armazenado é considerada aninhada.
Um exemplo de procedimento aninhado é mostrado no código a seguir:

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS BEGIN
    EXEC drop_external_table_if_exists @name;
    EXEC drop_external_file_format_if_exists @name;
    EXEC drop_external_data_source_if_exists @name;
END
```

Esse procedimento aceita um parâmetro que representa um nome e, em seguida, chama outros procedimentos para descartar os objetos com esse nome.
O pool SQL Synapse dá suporte a um máximo de oito níveis de aninhamento. Esse recurso é um pouco diferente de SQL Server. O nível de aninhamento no SQL Server é de 32.

A chamada de procedimento armazenado de nível superior é igual ao nível 1 de aninhamento.

```sql
EXEC clean_up 'mytest'
```

Se o procedimento armazenado também criar outra chamada EXEC, o nível de aninhamento aumentará para dois.

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

Se o segundo procedimento executar algum SQL dinâmico, o nível de aninhamento aumentará para três.

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    /* See full code in the previous example */
    EXEC sp_executesql @tsql = @drop_stmt;  -- This call is nest level 3
END
GO
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

> [!NOTE]
> No momento, o SQL Synapse não dá suporte a [@ @NESTLEVEL ](/sql/t-sql/functions/nestlevel-transact-sql?view=azure-sqldw-latest&preserve-view=true). É necessário rastrear o nível de aninhamento. É improvável que você exceda o limite de oito níveis de aninhamento, mas, se você fizer isso, será necessário trabalhar novamente em seu código para ajustar os níveis de aninhamento dentro desse limite.

## <a name="insertexecute"></a>INSERT..EXECUTE

O pool SQL provisionado Synapse não permite que você consuma o conjunto de resultados de um procedimento armazenado com uma instrução INSERT. Há uma abordagem alternativa que você pode usar. Para obter um exemplo, consulte o artigo em [tabelas temporárias](develop-tables-temporary.md) para o pool do SQL Synapse provisionado.

## <a name="limitations"></a>Limitações

Há alguns aspectos dos procedimentos armazenados Transact-SQL que não são implementados no SQL Synapse, como:

| Recurso/opção | Provisionado | Sem servidor |
| --- | --- |
| Procedimentos armazenados temporários | Não | Sim |
| Procedimentos armazenados numerados | Não | Não |
| Procedimentos armazenados estendidos | Não | Não |
| procedimentos armazenados de CLR | Não | Não |
| Opção de criptografia | Não | Sim |
| Opção de replicação | Não | Não |
| Parâmetros com valor de tabela | Não | Não |
| Parâmetros somente leitura | Não | Não |
| Parâmetros padrão | Não | Sim |
| Contextos de execução | Não | Não |
| instrução Return | Não | Sim |
| INSERIR EM.. EXEC | Não | Sim |

## <a name="next-steps"></a>Próximas etapas

Para obter mais dicas de desenvolvimento, confira [visão geral de desenvolvimento](develop-overview.md).
