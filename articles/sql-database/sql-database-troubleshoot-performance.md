---
title: "Monitoramento e ajuste de desempenho – Banco de Dados SQL do Azure | Microsoft Docs"
description: "Dicas de ajuste de desempenho no Banco de Dados SQL por meio de avaliação e melhoria."
services: sql-database
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
keywords: ajuste de desempenho de sql, ajuste de desempenho de banco de dados, dicas de ajuste de desempenho do sql, ajuste de desempenho de banco de dados sql
ms.assetid: eb7b3f66-3b33-4e1b-84fb-424a928a6672
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/13/2017
ms.author: v-shysun
ms.openlocfilehash: 39d85e7c1c45dd1bb28705ae06c4b5364b8baf37
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-and-performance-tuning"></a>Monitoramento e ajuste de desempenho

O Banco de Dados SQL do Azure é um serviço de dados flexível e gerenciado automaticamente no qual você pode facilmente monitorar o uso, adicionar ou remover recursos (CPU, memória, E/S), encontrar recomendações que podem melhorar o desempenho do banco de dados ou permitir que o banco de dados se adapte à carga de trabalho e otimize o desempenho automaticamente.

Este artigo fornece uma visão geral das opções de monitoramento e ajuste de desempenho disponíveis no Banco de Dados SQL do Azure.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="monitoring-and-troubleshooting-database-performance"></a>Monitorando e solucionando problemas de desempenho do banco de dados

O Banco de Dados SQL do Azure permite que você monitore facilmente o uso do banco de dados e identifique as consultas que podem causar problemas de desempenho. Monitore o desempenho do banco de dados usando o portal do Azure ou exibições do sistema. Você tem as seguintes opções para monitorar e solucionar problemas de desempenho do banco de dados:

1. No [portal do Azure](https://portal.azure.com), clique em **Bancos de dados SQL**, selecione o banco de dados e, depois, use o gráfico de Monitoramento para procurar recursos que estão atingindo o limite. O consumo de DTU é exibido por padrão. Clique em **Editar** para alterar o intervalo de tempo e os valores mostrados.
2. Use a [Análise de Desempenho de Consultas](sql-database-query-performance.md) para identificar as consultas que gastam a maioria dos recursos.
3. Você pode usar DMVs (exibições de gerenciamento dinâmico), Eventos Estendidos (`XEvents`) e o Repositório de Consultas no SSMS para obter parâmetros de desempenho em tempo real.

Consulte o [tópico de diretrizes de desempenho](sql-database-performance-guidance.md) para descobrir técnicas que você pode usar para melhorar o desempenho do Banco de Dados SQL do Azure, caso você identifique algum problema ao usar esses relatórios ou essas exibições.

> [!IMPORTANT] 
> Recomendamos que você sempre use a versão mais recente do Management Studio a fim de permanecer sincronizado com as atualizações no Microsoft Azure e no Banco de Dados SQL. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
>

## <a name="optimize-database-to-improve-performance"></a>Otimizar o banco de dados para melhorar o desempenho

O Banco de Dados SQL do Azure permite identificar oportunidades para melhorar e otimizar o desempenho de consultas sem alterar recursos examinando as [recomendações de ajuste de desempenho](sql-database-advisor.md). Índices ausentes e consultas precárias são motivos comuns de um desempenho ruim do banco de dados. Aplique essas recomendações de ajuste para melhorar o desempenho de sua carga de trabalho.
Você também pode permitir que o banco de dados SQL do Azure [otimize o desempenho das consultas automaticamente](sql-database-automatic-tuning.md), aplicando todas as recomendações identificadas e verificando se elas melhoram o desempenho do banco de dados. Use as seguintes opções para melhorar o desempenho do banco de dados:

1. Use o [Assistente do Banco de Dados SQL](sql-database-advisor-portal.md) para exibir recomendações para criar e remover índices, parametrizar consultas e corrigir problemas de esquema.
2. [Habilite o ajuste automático](sql-database-automatic-tuning-enable.md) e permita que o banco de dados SQL do Azure corrija os problemas de desempenho identificados automaticamente.

## <a name="improving-database-performance-with-more-resources"></a>Melhorando o desempenho do banco de dados com mais recursos

Por fim, se não houver nenhum item acionável que possa melhorar o desempenho do banco de dados, você poderá alterar a quantidade de recursos disponíveis no Banco de Dados SQL do Azure. Atribua mais recursos alterando a [camada de serviço](sql-database-service-tiers.md) de um banco de dados autônomo ou aumente as eDTUs de um pool elástico a qualquer momento.
1. Para bancos de dados independentes, é possível [alterar as camadas de serviço](sql-database-service-tiers.md) sob demanda para melhorar o desempenho do banco de dados.
2. Para vários bancos de dados, considere o uso de [pools elásticos](sql-database-elastic-pool-guidance.md) para dimensionar os recursos automaticamente.

## <a name="tune-and-refactor-application-or-database-code"></a>Ajustar e refatorar o código do aplicativo ou do banco de dados

Você pode alterar o código do aplicativo para usar o banco de dados de forma mais otimizada, alterar índices, forçar planos ou usar dicas para adaptar manualmente o banco de dados à sua carga de trabalho. Encontre algumas diretrizes e dicas para o ajuste manual e a reescrita do código no artigo [tópico de diretrizes de desempenho](sql-database-performance-guidance.md).


## <a name="next-steps"></a>Próximas etapas

- Para habilitar o ajuste automático no Banco de Dados SQL do Azure e permitir que o recurso de ajuste automático gerencie a carga de trabalho por completo, consulte [Habilitar o ajuste automático](sql-database-automatic-tuning-enable.md).
- Para usar o ajuste manual, examine as [Recomendações de ajuste no portal do Azure](sql-database-advisor-portal.md) e aplique manualmente aquelas que melhoram o desempenho de suas consultas.
- Altere os recursos que estão disponíveis no banco de dados alterando as [camadas de serviço do Banco de Dados SQL do Azure](sql-database-performance-guidance.md)