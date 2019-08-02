---
title: Usando procedimentos armazenados no SQL Data Warehouse do Azure | Microsoft Docs
description: Dicas para implementar procedimentos armazenados no SQL Data Warehouse do Azure para desenvolvimento de soluções.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/02/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 2c12a679ed5f0a1574deb34df8c0151e737d2d01
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479582"
---
# <a name="using-stored-procedures-in-sql-data-warehouse"></a>Usando procedimentos armazenados no SQL Data Warehouse
Dicas para implementar procedimentos armazenados no SQL Data Warehouse do Azure para desenvolvimento de soluções.

## <a name="what-to-expect"></a>O que esperar

O SQL Data Warehouse é compatível com muitos recursos T-SQL usados no SQL Server. Mais importante, há recursos específicos de expansão que você pode usar para maximizar o desempenho da sua solução.

No entanto, para manter a escala e o desempenho do SQL Data Warehouse também há alguns recursos e funcionalidades que apresentam diferenças de comportamento e outros que não têm suporte.


## <a name="introducing-stored-procedures"></a>Apresentação dos procedimentos armazenados
Os procedimentos armazenados são uma ótima maneira de encapsular o código SQL, armazenando-o perto de seus dados no data warehouse. Os procedimentos armazenados ajudam os desenvolvedores a modularizarem suas soluções encapsulando o código em unidades gerenciáveis; facilitando a maior reutilização do código. Cada procedimento armazenado também pode aceitar parâmetros para torná-lo ainda mais flexível.

O SQL Data Warehouse fornece uma implementação simplificada e otimizada de procedimentos armazenados. A maior diferença em comparação ao SQL Server é que o procedimento armazenado não é um código pré-compilado. Em data warehouses, o tempo de compilação é pequeno em comparação com o tempo necessário para executar consultas em grandes volumes de dados. É mais importante garantir que o código do procedimento armazenado seja otimizado corretamente para grandes consultas. A meta é poupar horas, minutos e segundos, não milissegundos. Portanto, é mais útil pensar em procedimentos armazenados como contêineres para a lógica SQL.     

Quando o SQL Data Warehouse executa o procedimento armazenado, as instruções SQL são analisadas, traduzidas e otimizadas no tempo de execução. Durante esse processo, cada instrução é convertida em consultas distribuídas. O código SQL executado nos dados é diferente da consulta enviada.

## <a name="nesting-stored-procedures"></a>Aninhamento de procedimentos armazenados
Quando os procedimentos armazenados chamam outros procedimentos armazenados ou executam SQL dinâmico, a invocação interna de código ou procedimento armazenado é considerada aninhada.

O SQL Data Warehouse é compatível com no máximo oito níveis de aninhamento. Isso é um pouco diferente do SQL Server. O nível de aninhamento no SQL Server é de 32.

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

Observe que, no momento, o SQL Data Warehouse não dá suporte é compatível com [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql). É necessário rastrear o nível de aninhamento. É improvável que você exceda o limite de oito níveis de aninhamento, mas, se você fizer isso, será necessário trabalhar novamente em seu código para ajustar os níveis de aninhamento dentro desse limite.

## <a name="insertexecute"></a>INSERT..EXECUTE
O SQL Data Warehouse não permite que você consuma o conjunto de resultados de um procedimento armazenado com uma instrução INSERT. No entanto, há uma abordagem alternativa. Para obter um exemplo, consulte o artigo em [tabelas temporárias](sql-data-warehouse-tables-temporary.md). 

## <a name="limitations"></a>Limitações
Há alguns aspectos de procedimentos armazenados Transact-SQL que não são implementados no SQL Data Warehouse.

Elas são:

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
* instrução return

## <a name="next-steps"></a>Próximas etapas
Para obter mais dicas de desenvolvimento, confira [visão geral de desenvolvimento](sql-data-warehouse-overview-develop.md).

