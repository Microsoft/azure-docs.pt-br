---
title: Migrar sua solução para o SQL Data Warehouse | Microsoft Docs
description: Orientação de migração para levar sua solução até a plataforma SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
origin.date: 04/17/2018
ms.date: 03/25/2019
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: 04c921282d3591e7326d326c230bf72e7f5c1812
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776213"
---
# <a name="migrate-your-solution-to-azure-sql-data-warehouse"></a>Migrar sua solução para o SQL Data Warehouse do Azure
Veja o que envolve a migração de uma solução de banco de dados existente para o SQL Data Warehouse do Azure. 

## <a name="profile-your-workload"></a>Criar o perfil de sua carga de trabalho
Antes de migrar, convém verificar se o SQL Data Warehouse é a solução correta para sua carga de trabalho. O SQL Data Warehouse é um sistema distribuído projetado para executar uma análise em dados grandes.  A migração para o SQL Data Warehouse exige algumas alterações de design que não são muito difíceis de entender, mas que podem demorar algum tempo para serem implementadas. Se sua empresa exige um data warehouse corporativo, os benefícios valem a pena. No entanto, se você não precisar da potência do SQL Data Warehouse, é mais barato usar o SQL Server ou o Banco de Dados SQL do Azure.

Considere o uso do SQL Data Warehouse quando você:
- Tiver um ou mais Terabytes de dados
- Planejar executar análise em grandes quantidades de dados
- Precisar da capacidade de dimensionar a computação e o armazenamento 
- Quiser economizar nos custos pausando os recursos de computação quando não precisar deles.

Não use o SQL Data Warehouse para cargas de trabalho operacionais (OLTP) que têm:
- Alta frequência de leituras e gravações
- Grande quantidade de seleções singleton
- Muitos volumes de inserções de linha única
- Necessidades de processamento linha por linha
- Formatos incompatíveis (JSON, XML)

## <a name="plan-the-migration"></a>Planejar a migração

Depois que você decidiu migrar uma solução existente para o SQL Data Warehouse, é importante planejar a migração antes de começar. 

Uma meta do planejamento é garantir que seu código, os esquemas de tabela e seus dados sejam compatíveis com o SQL Data Warehouse. Há algumas diferenças de compatibilidade para solucionar entre seu sistema atual e o SQL Data Warehouse. Além disso, a migração de grandes quantidades de dados para o Azure leva tempo. Um planejamento cuidadoso acelera a obtenção de seus dados no Azure. 

Outro objetivo do planejamento é fazer ajustes de design para garantir que sua solução aproveite o alto desempenho de consultas que o SQL Data Warehouse foi projetado para fornecer. O design de data warehouses, de acordo com a escala, apresenta padrões de design diferentes. Portanto, as abordagens tradicionais nem sempre são as melhores. Embora você possa fazer alguns ajustes de design após a migração, fazer alterações logo no início do processo economizará tempo mais tarde.

Para executar uma migração bem-sucedida, você precisa migrar seus dados, seu código e os esquemas de tabela. Para obter orientação sobre esses tópicos de migração, consulte:

-  [Migrar seus esquemas](sql-data-warehouse-migrate-schema.md)
-  [Migrar seu código](sql-data-warehouse-migrate-code.md)
-  [Migrar seus dados](sql-data-warehouse-migrate-data.md). 

## <a name="next-steps"></a>Próximas etapas
A CAT (Equipe Consultiva para Clientes) também tem algumas diretrizes ótimas do SQL Data Warehouse, que publica por meio de blogs.  Dê uma olhada no artigo [Migrating data to Azure SQL Data Warehouse in practice][Migrating data to Azure SQL Data Warehouse in practice] (Migrando dados para o SQL Data Warehouse do Azure na prática) para obter diretrizes adicionais sobre a migração.

<!--Image references-->

<!--Article references-->

<!--MSDN references-->

<!--Other Web references-->
[Migrating data to Azure SQL Data Warehouse in practice]: https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-data-to-azure-sql-data-warehouse-in-practice/

<!--Update_Description: update meta properties, wording update-->