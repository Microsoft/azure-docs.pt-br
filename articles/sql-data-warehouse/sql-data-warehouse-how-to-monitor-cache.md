---
title: Otimizar seu cache Gen2 | Microsoft Docs
description: Saiba como monitorar seu cache Gen2 usando o portal do Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: performance
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 26791aecb2ca57b31358d3385d07230c73c84904
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474380"
---
# <a name="how-to-monitor-the-gen2-cache"></a>Como monitorar o cache Gen2
A arquitetura de armazenamento Gen2 divide automaticamente em camadas seus segmentos columnstore consultados com mais frequência em um cache que reside em SSDs baseados em NVMe projetado para data warehouses Gen2. O melhor desempenho é alcançado quando suas consultas recuperam segmentos que residem no cache. Este artigo descreve como monitorar e solucionar problemas de desempenho de consultas lentas determinando se sua carga de trabalho está aproveitando da forma ideal o cache Gen2.  
## <a name="troubleshoot-using-the-azure-portal"></a>Solucionar problemas usando o Portal do Azure
Você pode usar o Azure Monitor para exibir métricas do cache Gen2 para solucionar problemas de desempenho de consulta. Primeiro, acesse o portal do Azure e clique em Monitorar:

![Azure Monitor](./media/sql-data-warehouse-cache-portal/cache_0.png)

Selecione o botão de métricas e preencha as informações de **Assinatura**, **Grupo** **de Recursos**, **Tipo de recurso** e **Nome do recurso** de seu data warehouse.

As principais métricas para solucionar problemas do cache Gen2 são **Percentual de ocorrência no cache** e **Percentual de uso do cache**. Configure o gráfico de métricas do Azure para exibir essas duas métricas.

![Métricas de cache](./media/sql-data-warehouse-cache-portal/cache_1.png)


## <a name="cache-hit-and-used-percentage"></a>Ocorrência no cache e percentual de uso
A matriz a seguir descreve cenários baseados nos valores das métricas de cache:

|                                | **Alto percentual de ocorrência no cache** | **Baixo percentual de ocorrência no cache** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Alto percentual de uso do cache** |          Cenário 1           |          Cenário 2          |
| **Baixo percentual de uso do cache**  |          Cenário 3           |          Cenário 4          |

**Cenário 1:** Você está usando o cache de forma ideal. [Solucione problemas](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) de outras áreas que podem estar deixando suas consultas mais lentas.

**Cenário 2:** Seu conjunto de dados de trabalho atual não se ajusta ao cache, o que causa um percentual baixo de ocorrência no cache devido às leituras físicas. Considere aumentar o nível de desempenho e execute novamente sua carga de trabalho para popular o cache.

**Cenário 3:** É provável que sua consulta esteja sendo executada lentamente por motivos não relacionados ao cache. [Solucione problemas](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) de outras áreas que podem estar deixando suas consultas mais lentas. Você também pode considerar [reduzir verticalmente sua instância](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) para reduzir o tamanho do cache e economizar custos. 

**Cenário 4:** Você tinha um cache frio que poderia ser o motivo de lentidão da consulta. Considere a possibilidade de executar novamente a consulta, pois seu conjunto de dados de trabalho agora deve estar armazenado em cache. 

**Importante: Se o percentual de ocorrência no cache ou o percentual de uso do cache não estiver sendo atualizado depois de executar novamente sua carga de trabalho, seu conjunto de trabalho poderá já estar residindo na memória. Observe que somente tabelas de columnstore clusterizadas são armazenadas em cache.**

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o ajuste de desempenho de consultas geral, confira [Monitorar a execução de consulta](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor#monitor-query-execution).


<!--Image references-->

<!--Article references-->
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: https://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx
