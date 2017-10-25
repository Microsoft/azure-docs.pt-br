---
title: Migrar seu esquema para o SQL Data Warehouse | Microsoft Docs
description: "Dicas para migrar seu esquema para o SQL Data Warehouse do Azure para desenvolvimento de soluções."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 538b60c9-a07f-49bf-9ea3-1082ed6699fb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 10/31/2016
ms.author: joeyong;barbkess
ms.openlocfilehash: 07ca2321852e276502187e768177e7e82bdfd080
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-your-schemas-to-sql-data-warehouse"></a>Migrar seus esquemas para o SQL Data Warehouse
Diretrizes para migrar os esquemas do SQL para o SQL Data Warehouse. 

## <a name="plan-your-schema-migration"></a>Planejar a migração de esquema

Ao planejar uma migração, confira a [visão geral da tabela][table overview] para se familiarizar com as considerações de design de tabela, como estatísticas, distribuição, particionamento e indexação.  Também lista alguns [recursos de tabela sem suporte][unsupported table features] e suas soluções alternativas.

## <a name="use-user-defined-schemas-to-consolidate-databases"></a>Use esquemas definidos pelo usuário para consolidar os bancos de dados

Provavelmente, a carga de trabalho tem mais de um banco de dados. Por exemplo, um data warehouse do SQL Server pode incluir um banco de dados de preparo, um banco de dados do data warehouse e alguns bancos de dados do data mart. Nessa topologia, cada banco de dados é executado como uma carga de trabalho separada, com políticas de segurança separadas.

Por outro lado, o SQL Data Warehouse executa toda a carga de trabalho do data warehouse em um só banco de dados. Uniões cruzadas de banco de dados não são permitidas. Portanto, o SQL Data Warehouse espera que todas as tabelas usadas pelo data warehouse sejam armazenadas em um só banco de dados.

É recomendável usar esquemas definidos pelo usuário para consolidar sua carga de trabalho existente em um banco de dados. Para obter exemplos, confira [Esquemas definidos pelo usuário](sql-data-warehouse-develop-user-defined-schemas.md)

## <a name="use-compatible-data-types"></a>Usar tipos de dados compatíveis
Modifique os tipos de dados para serem compatíveis com o SQL Data Warehouse. Para obter uma lista de tipos de dados com e sem suporte, confira [tipos de dados][data types]. Esse tópico fornece soluções alternativas para os tipos sem suporte. Também fornece uma consulta para identificar os tipos existentes que não têm suporte no SQL Data Warehouse.

## <a name="minimize-row-size"></a>Minimizar o tamanho de linha
Para obter o melhor desempenho, minimize o tamanho de linha das tabelas. Como comprimentos de linha menores resultam em melhor desempenho, use os menores tipos de dados que funcionarem para os dados. 

Para a largura da linha de tabela, o PolyBase tem um limite de 1 MB.  Se você pretende carregar dados no SQL Data Warehouse com o PolyBase, atualize as tabelas para que as larguras de linha máximas sejam inferiores a 1 MB. 

<!--
- For example, this table uses variable length data but the largest possible size of the row is still less than 1 MB. PolyBase will load data into this table.

- This table uses variable length data and the defined row width is less than one MB. When loading rows, PolyBase allocates the full length of the variable-length data. The full length of this row is greater than one MB.  PolyBase will not load data into this table.  

-->

## <a name="specify-the-distribution-option"></a>Especifique a opção de distribuição
SQL Data Warehouse é um sistema de banco de dados distribuído. Cada tabela é distribuída ou replicada em todos os nós de Computação. Há uma opção de tabela que permite que você especifique como distribuir os dados. As opções são round robin, replicado ou hash distribuído. Cada um tem prós e contras. Se você não especificar a opção de distribuição, o SQL Data Warehouse usará round-robin como o padrão.

- Round robin é o padrão. É mais simples de usar e carrega os dados tão rápido quanto possível, mas junções exigirão a movimentação de dados, o que reduz o desempenho da consulta.
- O item replicado armazena uma cópia da tabela em cada nó de computação. Tabelas replicadas são eficazes porque não exigem a movimentação de dados para junções e agregações. Isso exige armazenamento adicional e, assim, funciona melhor para tabelas menores.
- O hash distribuído distribui as linhas em todos os nós por meio de uma função de hash. As tabelas de hash distribuída são a essência do SQL Data Warehouse, pois foram projetadas para oferecer alto desempenho de consultas em tabelas grandes. Essa opção requer planejamento para selecionar a melhor coluna na qual distribuir os dados. No entanto, se não escolher a melhor coluna na primeira vez, você poderá facilmente redistribuir os dados em uma coluna diferente. 

Para escolher a melhor opção de distribuição para cada tabela, confira [Tabelas distribuídas](sql-data-warehouse-tables-distribute.md).


## <a name="next-steps"></a>Próximas etapas
Depois de migrar com êxito o esquema do seu banco de dados para o SQL Data Warehouse, passe para um dos artigos a seguir:

* [Migrar seus dados][Migrate your data]
* [Migrar seu código][Migrate your code]

Para saber mais sobre as práticas recomendadas do SQL Data Warehouse, confira o artigo sobre as [práticas recomendadas][best practices].

<!--Image references-->

<!--Article references-->
[Migrate your code]: ./sql-data-warehouse-migrate-code.md
[Migrate your data]: ./sql-data-warehouse-migrate-data.md
[best practices]: ./sql-data-warehouse-best-practices.md
[table overview]: ./sql-data-warehouse-tables-overview.md
[unsupported table features]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[data types]: ./sql-data-warehouse-tables-data-types.md
[unsupported data types]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types

<!--MSDN references-->


<!--Other Web references-->
