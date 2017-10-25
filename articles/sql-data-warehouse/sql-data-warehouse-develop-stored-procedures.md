---
title: Procedimentos armazenados no SQL Data Warehouse | Microsoft Docs
description: "Dicas para implementar procedimentos armazenados no SQL Data Warehouse do Azure para desenvolvimento de soluções."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 9b238789-6efe-4820-bf77-5a5da2afa0e8
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: e42d80f0ca35f3fbb67389c66d072bc40d8a8d2c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="stored-procedures-in-sql-data-warehouse"></a>Procedimentos armazenados no SQL Data Warehouse
O SQL Data Warehouse oferece suporte a muitos recursos Transact-SQL encontrados no SQL Server. Mais importante, há recursos específicos de expansão que vamos aproveitar para maximizar o desempenho da sua solução.

No entanto, para manter a escala e o desempenho do SQL Data Warehouse também há alguns recursos e funcionalidades que apresentam diferenças de comportamento e outros que não têm suporte.

Este artigo explica como implementar procedimentos armazenados no SQL Data Warehouse.

## <a name="introducing-stored-procedures"></a>Apresentação dos procedimentos armazenados
Os procedimentos armazenados são uma ótima maneira de encapsular o código SQL, armazenando-o perto de seus dados no data warehouse. O encapsulamento do código em unidades gerenciáveis de procedimentos armazenados ajuda os desenvolvedores a modularizar suas soluções; facilitando a maior reutilização do código. Cada procedimento armazenado também pode aceitar parâmetros para torná-lo ainda mais flexível.

O SQL Data Warehouse fornece uma implementação simplificada e otimizada de procedimentos armazenados. A maior diferença em comparação ao SQL Server é que o procedimento armazenado não é um código pré-compilado. Normalmente, quando se trata de data warehouses, nos preocupamos menos com o tempo de compilação. É mais importante que o código do procedimento armazenado seja otimizado corretamente ao operar com grandes volumes de dados. O objetivo é economizar horas, minutos e segundos, não milissegundos. Portanto, é mais útil pensar em procedimentos armazenados como contêineres para a lógica SQL.     

Quando o SQL Data Warehouse executa o procedimento armazenado, as instruções SQL são analisadas, traduzidas e otimizadas no tempo de execução. Durante esse processo, cada instrução é convertida em consultas distribuídas. O código SQL realmente executado nos dados é diferente da consulta enviada.

## <a name="nesting-stored-procedures"></a>Aninhamento de procedimentos armazenados
Quando os procedimentos armazenados chamam outros procedimentos armazenados ou executam sql dinâmico, a invocação interna de código ou procedimento armazenado é considerada aninhada.

O SQL Data Warehouse oferece suporte a um máximo de 8 níveis de aninhamento. Isso é um pouco diferente do SQL Server. O nível de aninhamento no SQL Server é de 32.

A chamada de procedimento armazenado de nível superior é igual ao nível 1 de aninhamento

```sql
EXEC prc_nesting
```
Se o procedimento armazenado também fizer outra chamada EXEC, isso aumentará o nível de aninhamento para 2

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Se o segundo procedimento então executar algum sql dinâmico, isso aumentará o nível de aninhamento para 3

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Observe que atualmente o SQL Data Warehouse não dá suporte ao @@NESTLEVEL. Você precisará manter o controle de seu nível de aninhamento por conta própria. É improvável que você atinja o limite 8 de nível de aninhamento, mas, se atingir, será necessário trabalhar novamente seu código e "nivelá-lo" para que fique dentro do limite.

## <a name="insertexecute"></a>INSERT..EXECUTE
O SQL Data Warehouse não permite que você consuma o conjunto de resultados de um procedimento armazenado com uma instrução INSERT. No entanto, há uma abordagem alternativa.

Confira o seguinte artigo sobre [tabelas temporárias] para obter um exemplo de como fazer isso.

## <a name="limitations"></a>Limitações
Há alguns aspectos de procedimentos armazenados Transact-SQL que não são implementados no SQL Data Warehouse.

Eles são:

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
Para obter mais dicas de desenvolvimento, confira [visão geral de desenvolvimento][development overview].

<!--Image references-->

<!--Article references-->
[tabelas temporárias]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[development overview]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[nest level]: https://msdn.microsoft.com/library/ms187371.aspx

<!--Other Web references-->
