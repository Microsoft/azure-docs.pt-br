---
title: Otimizar o cache Gen2
description: Saiba como monitorar seu cache Gen2 usando o portal do Azure.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 041751b5b23dbb3153f1ae638303579a860c0e5b
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020156"
---
# <a name="how-to-monitor-the-adaptive-cache"></a>Como monitorar o cache adaptável

Este artigo descreve como monitorar e solucionar problemas de desempenho de consulta lento, determinando se sua carga de trabalho está aproveitando o cache adaptável para pools de SQL dedicados.

A arquitetura de armazenamento do pool do SQL dedicada coloca automaticamente em camadas os segmentos columnstore consultados com mais frequência em um cache que reside no SSDs baseado em NVMe. Você terá maior desempenho quando suas consultas recuperarem segmentos que residem no cache.
 
## <a name="troubleshoot-using-the-azure-portal"></a>Solucionar problemas usando o Portal do Azure

Você pode usar Azure Monitor para exibir as métricas de cache para solucionar problemas de desempenho de consulta. Primeiro, acesse a portal do Azure e clique em **Monitor**, **métricas** e **+ Selecione um escopo**:

![Captura de tela mostra selecionar um escopo selecionado de métricas no portal do Azure.](./media/sql-data-warehouse-how-to-monitor-cache/cache-0.png)

Use as barras de pesquisa e suspensa para localizar seu pool SQL dedicado. Em seguida, selecione aplicar.

![Captura de tela mostra o painel Selecionar um escopo onde você pode selecionar seu data warehouse.](./media/sql-data-warehouse-how-to-monitor-cache/cache-1.png)

As principais métricas para solução de problemas de cache são **percentual de impacto de cache** e percentual de **cache usado**. Selecione **porcentagem de acesso ao cache** e, em seguida, use o botão **Adicionar métrica** para adicionar o **percentual de cache usado**. 

![Métricas de cache](./media/sql-data-warehouse-how-to-monitor-cache/cache-2.png)

## <a name="cache-hit-and-used-percentage"></a>Ocorrência no cache e percentual de uso

A matriz a seguir descreve cenários baseados nos valores das métricas de cache:

|                                | **Alto percentual de ocorrência no cache** | **Baixo percentual de ocorrência no cache** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Alto percentual de uso do cache** |          Cenário 1           |          Cenário 2          |
| **Baixo percentual de uso do cache**  |          Cenário 3           |          Cenário 4          |

**Cenário 1:** você está usando o cache de forma ideal. [Solucionar problemas](sql-data-warehouse-manage-monitor.md) de outras áreas que podem estar atrasando suas consultas.

**Cenário 2:** seu conjunto de dados de trabalho atual não cabe no cache, o que causa um percentual baixo de ocorrência no cache devido a leituras físicas. Considere aumentar o nível de desempenho e execute novamente sua carga de trabalho para popular o cache.

**Cenário 3:** é provável que sua consulta esteja sendo executada lentamente por motivos não relacionados ao cache. [Solucionar problemas](sql-data-warehouse-manage-monitor.md) de outras áreas que podem estar atrasando suas consultas. Você também pode considerar [reduzir verticalmente sua instância](sql-data-warehouse-manage-monitor.md) para reduzir o tamanho do cache e economizar custos. 

**Cenário 4:** você tinha um cache frio que poderia ser o motivo de sua consulta ser lenta. Considere a possibilidade de executar novamente a consulta, pois seu conjunto de dados de trabalho agora deve estar armazenado em cache. 

> [!IMPORTANT]
> Se a porcentagem de acesso ao cache ou o percentual de cache usado não estiver atualizando após a reexecução da carga de trabalho, seu conjunto funcional já poderá estar residindo na memória. Somente as tabelas columnstore clusterizadas são armazenadas em cache.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o ajuste de desempenho de consultas geral, confira [Monitorar a execução de consulta](sql-data-warehouse-manage-monitor.md#monitor-query-execution).
