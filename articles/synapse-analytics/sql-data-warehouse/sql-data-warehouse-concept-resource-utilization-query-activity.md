---
title: Gerenciamento e monitoramento – atividade de consulta, utilização de recursos
description: Saiba quais recursos estão disponíveis para gerenciar e monitorar o Azure Synapse Analytics. Use o portal do Azure e DMVs (Exibições de Gerenciamento Dinâmico) para entender a atividade de consulta e a utilização de recursos do data warehouse.
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/09/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 96a076ef144af1d32e9ef3ec2903557b8edff006
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100586707"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-synapse-analytics"></a>Monitorando a atividade de consulta e a utilização de recursos no Azure Synapse Analytics

O Azure Synapse Analytics oferece uma rica experiência de monitoramento no portal do Azure para gerar insights sobre a carga de trabalho do data warehouse. O portal do Azure é a ferramenta recomendada ao monitorar seu data warehouse, pois ele fornece períodos de retenção configuráveis, alertas, recomendações e gráficos e painéis personalizáveis para métricas e logs. O portal também permite a integração com outros serviços de monitoramento do Azure, como o Azure Monitor (logs) com análise de logs para fornecer uma experiência de monitoramento holística não apenas para seu data warehouse, mas também para toda a plataforma de análise do Azure para uma experiência de monitoramento integrada. Esta documentação descreve quais recursos de monitoramento estão disponíveis para otimizar e gerenciar sua plataforma de análise com o SQL do Synapse.

## <a name="resource-utilization"></a>Utilização de recursos

As seguintes métricas estão disponíveis no portal do Azure para o SQL do Synapse. Essas métricas são exibidas no [Azure Monitor](../../azure-monitor/data-platform.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json#metrics).

| Nome da métrica             | Descrição                                                  | Tipo de agregação |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| Percentual de CPU          | Utilização da CPU em todos os nós para o data warehouse      | Avg, Min, Max    |
| Porcentagem de E/S de dados      | Utilização de E/S em todos os nós para o data warehouse       | Avg, Min, Max    |
| Porcentagem de memória       | Utilização de memória (SQL Server) em todos os nós para o data warehouse | Avg, Min, Max   |
| Consultas ativas          | Número de consultas ativas em execução no sistema             | SUM              |
| Consultas na fila          | Número de consultas na fila aguardando para iniciar a execução          | SUM              |
| Conexões bem sucedidas  | Número de conexões bem-sucedidas (logons) com o banco de dados | Sum, Count       |
| Conexões com falha      | Número de conexões com falha (logons) com o banco de dados | Sum, Count       |
| Bloqueado pelo firewall     | Número de logons para o data warehouse que foram bloqueados     | Sum, Count       |
| Limite de DWU               | Objetivo de nível de serviço do data warehouse                | Avg, Min, Max    |
| Porcentagem de DWU          | Máximo entre o percentual de CPU e o percentual de E/S de dados        | Avg, Min, Max    |
| DWU usado                | Limite de DWU * percentual de DWU                                   | Avg, Min, Max    |
| Percentual de ocorrência no cache    | (ocorrências no cache/perda no cache) * 100, em que ocorrências no cache é a soma de todas as ocorrências de segmentos columnstore no cache SSD local e a perda no cache são as perdas de segmentos columnstore no cache SSD local somadas entre todos os nós | Avg, Min, Max    |
| Percentual de cache usado   | (cache usado / capacidade de cache) * 100, em que o cache usado é a soma de todos os bytes no cache SSD local entre todos os nós e a capacidade de cache é a soma da capacidade de armazenamento do cache SSD local entre todos os nós | Avg, Min, Max    |
| Porcentagem de local de tempdb | Utilização de tempdb local em todos os nós de computação - os valores são emitidos a cada cinco minutos | Avg, Min, Max    |

Pontos a serem considerados ao exibir métricas e definir alertas:

- A DWU usada apresenta apenas uma **representação de alto nível de uso** pelo pool de SQL e não deve ser um indicador abrangente de utilização. Para determinar se é necessário escalar ou reduzir verticalmente, considere todos os fatores que podem ser afetados pela DWU, como simultaneidade, memória, tempdb e capacidade de cache adaptável. É recomendável [executar sua carga de trabalho em diferentes configurações da DWU](sql-data-warehouse-manage-compute-overview.md#finding-the-right-size-of-data-warehouse-units) para determinar o que funciona melhor para atender aos seus objetivos de negócios.
- Conexões com falha e bem-sucedidas são relatadas para um determinado data warehouse-não para o próprio servidor.
- A porcentagem de memória reflete a utilização mesmo que o data warehouse esteja no estado ocioso, ele não reflete o consumo de memória da carga de trabalho ativa. Use e acompanhe essa métrica junto com outras (tempdb, cache Gen2) para tomar uma decisão holística sobre se o dimensionamento da capacidade de cache adicional aumentará o desempenho da carga de trabalho para atender às suas necessidades.

## <a name="query-activity"></a>Consultar atividade

Para uma experiência de programação ao monitorar o SQL do Synapse por meio do T-SQL, o serviço fornece um conjunto de DMVs (Exibições de Gerenciamento Dinâmico). Essas exibições são úteis ao ativamente resolver problemas e identificar gargalos de desempenho com sua carga de trabalho.

Para exibir a lista de DMVs que o SQL do Synapse fornece, confira esta [documentação](../sql/reference-tsql-system-views.md#dedicated-sql-pool-dynamic-management-views-dmvs). 

## <a name="metrics-and-diagnostics-logging"></a>Log de diagnósticos e métricas 

As métricas e os logs podem ser exportados para o Monitor do Azure, especificamente o componente de [log do Azure Monitor](../../azure-monitor/logs/log-query-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) e podem ser acessados programaticamente por meio das [consultas de logs](../../azure-monitor/logs/log-analytics-tutorial.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json). A latência de log para SQL do Synapse é de cerca de 10-15 minutos. Para obter mais detalhes sobre os fatores que afetam a latência, visite a documentação a seguir.

## <a name="next-steps"></a>Próximas etapas

O guia de instruções a seguir descreve cenários e casos de uso comuns ao monitorar e gerenciar seu data warehouse:

- [Monitorar sua carga de trabalho do data warehouse com DMVs](sql-data-warehouse-manage-monitor.md)