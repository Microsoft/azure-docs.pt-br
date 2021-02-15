---
title: Uso de procedimentos armazenados
description: Dicas para desenvolver soluções implementando procedimentos armazenados para pools de SQL dedicados no Azure Synapse Analytics.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: e28eeac131c737d673cac947a3fda30239180a62
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98673579"
---
# <a name="using-stored-procedures-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Usando procedimentos armazenados para pools dedicados do SQL no Azure Synapse Analytics

Este artigo fornece dicas para desenvolver soluções dedicadas do pool SQL implementando procedimentos armazenados.

## <a name="what-to-expect"></a>O que esperar

O pool SQL dedicado dá suporte a muitos dos recursos do T-SQL que são usados no SQL Server. Mais importante, há recursos específicos de expansão que você pode usar para maximizar o desempenho da sua solução.

Além disso, para ajudá-lo a manter a escala e o desempenho do pool SQL dedicado, há recursos e funcionalidades adicionais que têm diferenças comportamentais.

## <a name="introducing-stored-procedures"></a>Apresentação dos procedimentos armazenados

Os procedimentos armazenados são uma ótima maneira de encapsular seu código SQL, que é armazenado perto de seus dados de pool do SQL dedicados. Os procedimentos armazenados também ajudam os desenvolvedores a modularizar suas soluções encapsulando o código em unidades gerenciáveis, facilitando assim a reutilização de código maior. Cada procedimento armazenado também pode aceitar parâmetros para torná-lo ainda mais flexível.

O pool SQL dedicado fornece uma implementação de procedimento armazenado simplificado e simplificado. A maior diferença em comparação com SQL Server é que o procedimento armazenado não é um código pré-compilado.

Em geral, para data warehouses, o tempo de compilação é pequeno em comparação ao tempo necessário para executar consultas em grandes volumes de dados. É mais importante garantir que o código do procedimento armazenado seja otimizado corretamente para consultas grandes.

> [!TIP]
> A meta é poupar horas, minutos e segundos, não milissegundos. Portanto, é útil considerar os procedimentos armazenados como contêineres para a lógica do SQL.

Quando um pool SQL dedicado executa o procedimento armazenado, as instruções SQL são analisadas, traduzidas e otimizadas em tempo de execução. Durante esse processo, cada instrução é convertida em consultas distribuídas. O código SQL executado nos dados é diferente da consulta enviada.

## <a name="nesting-stored-procedures"></a>Aninhamento de procedimentos armazenados

Quando os procedimentos armazenados chamam outros procedimentos armazenados ou executam SQL dinâmico, a invocação interna de código ou procedimento armazenado é considerada aninhada.

O pool SQL dedicado dá suporte a um máximo de oito níveis de aninhamento. Por outro lado, o nível de aninhamento no SQL Server é 32.

A chamada de procedimento armazenado de nível superior é igual ao nível 1 de aninhamento.

```sql
EXEC prc_nesting
```

Se o procedimento armazenado também criar outra chamada EXEC, o nível de aninhamento aumentará para dois.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```

Se o segundo procedimento executar algum SQL dinâmico, o nível de aninhamento aumentará para três.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

No momento, o pool SQL dedicado não dá suporte a [@ @NESTLEVEL ](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Assim, você precisa acompanhar o nível de aninhamento. É improvável que você exceda o limite de oito níveis de aninhamento. Mas, se você fizer isso, precisará retrabalhar seu código para se ajustar aos níveis de aninhamento dentro desse limite.

## <a name="insertexecute"></a>INSERT..EXECUTE

O pool SQL dedicado não permite que você consuma o conjunto de resultados de um procedimento armazenado com uma instrução INSERT. Há, no entanto, uma abordagem alternativa que você pode usar. Para obter um exemplo, consulte o artigo em [tabelas temporárias](sql-data-warehouse-tables-temporary.md).

## <a name="limitations"></a>Limitações

Há alguns aspectos dos procedimentos armazenados Transact-SQL que não são implementados no pool SQL dedicado, que são os seguintes:

* procedimentos armazenados temporariamente
* procedimentos armazenados numerados
* procedimentos armazenados estendidos
* procedimentos armazenados de CLR
* opção de criptografia
* opção de replicação
* parâmetros com valor de tabela
* parâmetros somente leitura
* parâmetros padrão
* contextos de execução
* Instrução return

## <a name="next-steps"></a>Próximas etapas

Para obter mais dicas de desenvolvimento, confira [visão geral de desenvolvimento](sql-data-warehouse-overview-develop.md).
