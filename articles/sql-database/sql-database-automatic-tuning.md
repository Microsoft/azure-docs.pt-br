---
title: "Banco de Dados SQL do Microsoft Azure - ajuste automático | Microsoft Docs"
description: "O Banco de Dados SQL do Microsoft Azure analisa a consulta SQL e automaticamente se adapta à carga de trabalho do usuário."
services: sql-database
documentationcenter: 
author: jovanpop-msft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/19/2017
ms.author: jovanpop
ms.openlocfilehash: 64abcf680199938c54be5005ee96670a8e580270
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Ajuste automático no Banco de Dados SQL do Microsoft Azure

O Banco de Dados SQL do Microsoft Azure é um serviço de dados totalmente gerenciado que monitora as consultas que são executadas no banco de dados e melhora automaticamente o desempenho da carga de trabalho do banco de dados. O Banco de Dados SQL do Azure tem um mecanismo de inteligência interno de [Ajuste Automático](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) que pode ajustar e melhorar o desempenho de suas consultas adaptando dinamicamente o banco de dados para sua carga de trabalho automaticamente. O ajuste automático no Banco de Dados SQL do Azure talvez seja um dos recursos mais importantes que você pode habilitar no Banco de Dados SQL do Azure para otimizar o desempenho das suas consultas.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-in-the-Enterprise/Enabling-Azure-SQL-Database-Auto-Tuning-at-Scale-for-Microsoft-IT/player]
>

## <a name="automatic-tuning-options"></a>Opções de ajuste automático

As opções de [Ajuste automático](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) disponíveis no Banco de Dados SQL do Microsoft Azure são:
 1. **CREATE INDEX** que identifica os índices que podem melhorar o desempenho da carga de trabalho, cria os índices e verifica que eles melhoram o desempenho das consultas.
 2. **DROP INDEX** que identifica os índices duplicados e redundantes e índices que não foram usados em um longo período de tempo.
 3. **PLAN REGRESSION CORRECTION** que identifica as consultas SQL que estão usando o plano de execução que são mais lentos do que um bom plano anterior e usa o último plano bom conhecido em vez do plano retornado.

O Banco de Dados SQL do Microsoft Azure identifica recomendações **CREATE INDEX**, **DROP INDEX** e **PLAN REGRESSION CORRECTION** que podem otimizar seu banco de dados e as mostra no Portal do Azure. Encontre mais informações sobre a identificação de índices que devem ser alterados em [Encontrar recomendações de índice no portal do Azure](sql-database-advisor-portal.md). Você pode aplicar manualmente as recomendações usando o portal ou permitir que o Banco de Dados SQL do Microsoft Azure aplique automaticamente as recomendações, monitore a carga de trabalho após a alteração e verifique se a recomendação melhorou o desempenho da carga de trabalho.

As opções de ajuste automático podem ser ativadas ou desativadas de forma independente por banco de dados, ou podem ser configuradas no servidor lógico e aplicadas em cada banco de dados que herda as configurações do servidor. É um método recomendável configurar as opções de [Ajuste automático](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) no servidor e herdar as configurações nos bancos de dados no servidor para configurar o ajuste automático porque ele simplifica o gerenciamento das opções de ajuste automático em um número grande de bancos de dados.

Consulte este artigo para obter as etapas para [habilitar o ajuste automático](sql-database-automatic-tuning-enable.md) usando o portal do Azure.

## <a name="next-steps"></a>Próximas etapas

- Para habilitar o ajuste automático no Banco de Dados SQL do Azure e permitir que o recurso de ajuste automático gerencie a carga de trabalho por completo, consulte [Habilitar o ajuste automático](sql-database-automatic-tuning-enable.md).
- Para usar o ajuste manual, examine as [Recomendações de ajuste no portal do Azure](sql-database-advisor-portal.md) e aplique manualmente aquelas que melhoram o desempenho de suas consultas.
- Leia mais sobre inteligência interna que ajusta o [Banco de Dados SQL do Microsoft Azure](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Leia mais sobre [Ajuste automático](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) no Banco de Dados SQL do Microsoft Azure e SQL Server 2017.
